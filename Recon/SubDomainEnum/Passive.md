# Subfinder 


- Enumerate 
 ```bash
 sublist3r -d yahoo.com
```


- Enumerate from text file 
	- where ur domains are saved in this file
```bash
subfinder -dL Domains.txt
``` 

- save output to a file
```bash
subfinder -d yahoo.com results.txt
```

- always SubDomanEnum of SubDomains


---
# AssetFinder
- Enum
```bash
assetfinder --subs-only yahoo.com
```


---
# knockpy
- Enum
```bash
knockpy yahoo.com
```

- To ignore http(s) responses with specific code, you can use the `--no-http-code` followed by the code list `404 500 530`. With the `--no-ip` option you can ignore ip list `192.168.1.100 192.168.101 192.168.1.102`


**- there is some filtration need to be done here**  

---
# Sublist3r 
- To list all the basic options and switches use -h switch:

`python sublist3r.py -h`

- To enumerate subdomains of specific domain:

`python sublist3r.py -d example.com`

- To enumerate subdomains of specific domain and show only subdomains which have open ports 80 and 443 :

`python sublist3r.py -d example.com -p 80,443`

- To enumerate subdomains of specific domain and show the results in realtime:

`python sublist3r.py -v -d example.com`

- To enumerate subdomains and enable the bruteforce module:

`python sublist3r.py -b -d example.com`

- To enumerate subdomains and use specific engines such Google, Yahoo and Virustotal engines

`python sublist3r.py -e google,yahoo,virustotal -d example.com`


---
# Amass 
