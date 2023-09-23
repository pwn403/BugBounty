# Directory Traversal
## Introduction 
Path traversal is also known as directory traversal. These vulnerabilities enable an attacker to read arbitrary files on the server that is running an application. This might include:

- Application code and data.
- Credentials for back-end systems.
- Sensitive operating system files.


## Where to Find
- spot all the requests that interacts with the files on the server 
- use burp active scan
- try some LFI and RFI (will be discussed later on)

## How to exploit
### File path traversal, simple case
- Determine whether the server is linux on win (make use of responses, use wapalizer extenstion)
- try simple payload for relative path
```
filename=../../../../../../etc/passwd
```
- absolute path 
```
filename=/etc/passwd
```
- so the functionality may be implemented using relative or absolute path 
###  traversal sequences blocked with absolute path bypass
- simply use absolute path 
```
filename=/etc/passwd
```
### traversal sequences stripped non-recursively
- if ../ is sanitized non recursevly 
```
filename=..././..././..././etc/passwd
```
### traversal sequences stripped with superfluous URL-decode
- some times there is a client side validation, so do url encoding, and double it , it may bypass the defense 
### validation of start of path
```
filename=/var/www/images/../../../etc/passwd
```
### validation of file extension with null byte bypass
- what is null byte ? 
	- represent an end of a string 
	- %00 
	- when the server reads it, it will cut any thing after it
```
filename=/etc/passwd%00.png
```