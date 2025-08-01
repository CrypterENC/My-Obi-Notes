## Scope :

- **It defines the range of assets that the org is explicitly inviting security researchers to assess for vulnerabilities.**
## Out of Scope :

- **Its refers to the assets that are explicitly off - limits for security researches participating in the bug bounty program.**
## Why is Scoping Important :

- It helps to set Legal and ethical boundaries.
- Helps in Resource Allocation.
- Practical reasons.
- Fairness.
## In-scope and Out of scope :

```Bash
Rule 1: Most Specific domain is the one that should be adhered to,
	Eg:
		- In-Scope: *.example.com
		- Out of Scope: sub.exmaple.com
		
		-> sub.example.com is out of scope; www.example.com is in-scope;
			 another.sub.example.com is in-scope; In-scope: sub.example.com
			 
		- Out of Scope: *.exampler.com
		-> sub.example.com is in-scope; www.example.com is out of scope;
			 another.sub.example.com is out of scope.
			 
Rule 2: Wildcard (*) character appies to none, one or more (without limit)
				subdomains.
		Eg:
			- In-Scope: *.sub.example.com
			-> sub.example.com is in-scope; another.sub.example.com is in-scope;
				 example.com is out of scope.
```

## Duplicates Bugs :

- **Orgs award the bounty to the initial reporter**
- **Knowingly reporting a duplicate report is unethical.**
## Community Code of Conduct :

- **Disclosure Terms**
- **Collaboration**
- **Asking for updates**
- **OOS Submission**
- **Use of illegal or cracked software.**