# Business logic Vulns 
## Introduction 
flaws in the design and implementation of an application that allow an attacker to elicit unintended behavior. This potentially enables attackers to manipulate legitimate functionality to achieve a malicious goal
## How to find ? 
- Map the application. Make note of each and every component in the application and how it operates.
- For each component determine : 
	- The potential business flow.
	- The assumptions that could have been made by the developers / architects during the design phase.
- Test each component for all possible use cases that are outside of the intended business flow.
## How to exploit ?
1. check if u can manipulate the orders prices using burp 
	1. make the price +ve or 0 or -ve
2. try to answer these questions to have better sight on the app
	1. Are there any limits that are imposed on the data?
	2. What happens when you reach those limits?
	3. Is any transformation or normalization being performed on your input?
3. if u can't manipulate the prices, try to add -ve amount of quantities, or quantities like 1 million 
4. if you find one form on the target website that fails to safely handle unconventional input, it's likely that other forms will have the same issues
5. if ur account must be in a group of admins, say user@admin.com, and u can't create an account with this email, 
	1. try to create a user with normal email, then look for email update functionality and update it. it may work,
	2. try to login without confirming the email
6. u may apply the same coupon many times 
7. if u have 2 coupons, submit coupon 1 then coupon 2, then resubmit coupon 1 again, the server may check the last coupon u entered only
8. In some cases, the more quantities u add, the checkout will become minus 
9. In registration, there might not be a limitation on the length of input, so try to submit large block of inputs and a truncate may occur, use it
10. if it's required username and pass to login, try to intercept the request and delete the pass param, it may work
11. Only remove one parameter at a time to ensure all relevant code paths are reached.
12. Try deleting the name of the parameter as well as the value. The server will typically handle both cases differently.
13. Follow multi-stage processes through to completion. Sometimes tampering with a parameter in one step will have an effect on another step further along in the workflow.
14. ==submit requests in an unintended sequence==