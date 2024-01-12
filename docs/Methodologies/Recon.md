# Recon the target

- Organization
	- Goals
	- Mergers and Acquisitions
	- Projects and Products
	- Recent news
- Infrastructure
	- IP Addresses
	- Hostnames
	- Software & Hardware
- Employees
	- Usernames
	- Email addresses
	- Breached credentials
	- Roles

# Organization Recon

- Information on the Organization
- Wikipedia
- Press Releases and Annual Reports
- Open Job
- Gather Competitive Intelligence

# Infrastructure Recon

- IP address assinment whois databases
	- whois
- DNS and host names
	- dig @[server] [name] [type]
	- zone transfer `dig @10.10.10.60 domain.tld -t AXFR`
	- `dnsrecon -d domain.tld -t type`
	- dnsdumpster.com
- google dorks
	- site:domain.tld -site:notuseful.domain.tld
	- intitle:index.of passwd
	- inurl:viewtopic.php
	- filetype:docx,xlsx
	- Google Hacking Database
- Shodan
- BuiltWith / Wappalyzer
- Hostname Information

# User Recon

- Metadata
	- exiftool
	- strings
- Emails / Usernames / Patterns
	- hunter.io
	- Breached data
		- torrent
		- dehashed
		- intelx
		- pwndb (https://pwndb2am4tzkvold.tor2web.io/)
- Social media
	- Linkedin
		- LinkedInt
		- crosslinked
- GatherContacts (Burp extension - to check)
- Automated OSINT tool
	- SpiderFoot (add API keys for better results)
