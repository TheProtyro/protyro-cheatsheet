### IP

```
whois <ip>
```

### Domain 

```
dig @<server> <name> <type>
```

- https://dnsdumpster.com/

### File

```
exiftool <file>
strings <file>
```

### Pseudo

```
sherlock <pseudo>
```

### LinkedIn

```
python3 crosslinked.py -f '{first}.{last}@domain.com' company_name
python3 crosslinked.py -f 'domain\{f}{last}' -t 45 -j 1 company_name
```

### Google Dorks

```
site:domain.tld -site:notuseful.domain.tld
intitle:index.of passwd
inurl:viewtopic.php
filetype:docx,xlsx
```

- https://www.exploit-db.com/google-hacking-database

```
metagoofil -d <domain> -t <doc,pdf,xls,ppt,odp,ods,docx,xslx,pptx> -l 200 -n 5 -o <out> -w
```

### Shodan

- https://www.shodan.io/
- https://beta.shodan.io/search/filters
- https://beta.shodan.io/search/examples

### Breached credentials

- https://pwndb2am4tzkvold.tor2web.io/ (free)
- https://intelx.io/ (paid)