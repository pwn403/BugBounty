# Information Disclosure 
## Introduction 
when a website unintentionally reveals
sensitive information to its users.
## How to find ? 
- Understand and be able to recognize sensitive data 
- everywhere 
- fuzz the app
## How to exploit ? 
1. Revealing the names of hidden directories, their structure, and their contents via a `robots.txt` or `sitemap.xml` file 
2. directory listing, Web servers can be configured to automatically list the contents of directories that do not have an index page present
3. Developers comments
4. Error messages, you should pay close attention to all error messages you encounter during auditing
5. if u found backup.php => try ~backup.php , or change the extension 
6. check the TRACE method 

- Providing access to source code files via temporary backups
- Explicitly mentioning database table or column names in error messages
- Unnecessarily exposing highly sensitive information, such as credit card details
- Hard-coding API keys, IP addresses, database credentials, and so on in the source code
- Hinting at the existence or absence of resources, usernames, and so on via subtle differences in application behavior