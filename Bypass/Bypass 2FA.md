
1. Response Manipulation

The response is
```
HTTP/1.1 404 Not Found
...
{"code": false}
```
Try this to bypass
```
HTTP/1.1 404 Not Found
...
{"code": true}
```

2. Status Code Manipulation

The response is
```
HTTP/1.1 404 Not Found
...
{"code": false}
```
Try this to bypass
```
HTTP/1.1 200 OK
...
{"code": false}
```

3. 2FA Code Leakage in Response

Always check the response!
```
POST /req-2fa/
Host: vuln.com
...
email=victim@gmail.com
```
The response is
```
HTTP/1.1 200 OK
...
{"email": "victim@gmail.com", "code": "101010"}
```

4. JS File Analysis => Rare but some JS Files may contain info about the 2FA Code, worth giving a shot

5. 2FA Code Reusability => Same code can be reused

6. Lack of Brute-Force Protection => Possible to brute-force any length 2FA Code

7. Missing 2FA Code Integrity Validation => Code for any user account can be used to bypass the 2FA
```
POST /2fa/
Host: vuln.com
...
email=attacker@gmail.com&code=382923
```
```
POST /2fa/
Host: vuln.com
...
email=victim@gmail.com&code=382923
```

8. CSRF on 2FA Disabling => No CSRF Protection on disabling 2FA, also there is no auth confirmation
9. Password Reset Disable 2FA => 2FA gets disabled on password change/email change
10. Backup Code Abuse => Bypassing 2FA by abusing the Backup code feature
- Use the above mentioned techniques to bypass Backup Code to remove/reset 2FA reset restrictions
11. Clickjacking on 2FA Disabling Page => Iframing the 2FA Disabling page and social engineering victim to disable the 2FA
12. Iframing the 2FA Disabling page and social engineering victim to disable the 2FA => If the session is already hijacked and there is a session timeout vulnerbility
13. Bypass 2FA with null or 000000

Enter the code 000000 or null to bypass 2FA protection.

Steps:-

1. Enter “null” in 2FA code

2. Enter 000000 in 2FA code

3. Send empty code - Someone found this in grammarly

4. Open new tab in same browser and check if other API endpoints are accessible without entering 2FA

14. Google Authenticator Bypass

Steps:-

1) Set-up Google Authenticator for 2FA

2) Now, 2FA is enabled

3) Go on password reset page and change your password

4) If you are website redirect you to your dashboard then 2FA (Google Authenticator) is bypassed

15. Bypassing OTP in registration forms by repeating the form submission multiple times using repeater

Steps :-

1) Create an account with a non-existing phone number

2) Intercept the Request in BurpSuite

3) Send the request to the repeater and forward

4) Go to Repeater tab and change the non-existent phone number to your phone number

5) If you got an OTP to your phone, try using that OTP to register that non-existent number