---
title: No Shake - InCTF Internationals 2020
date: 2020-08-04 21:08:25
author: m0n574 
author_url: https://twitter.com/m0n574
categories:
  - Web Exploitation
  - TLS
Tags:
  - Web Exploitation
  - InCTFi
---

**tl;dr**

+ Extract key from the admin by STARTTLS downgrade on the message
+ Deserialize using references to get next phase 
+ Deserialization to RCE to get flag 

<!--more-->

**Challenge points**: 900
**No. of solves**: 21
**Challenge Author**: [m0n574](https://twitter.com/m0n574)

## Challenge description

The admin does not like to shake hands, virus!

## Initial analysis

We are provided with a service where you send a mail to the admin on what you want to hear from him. Once we click send, we receive few response codes and an encrypted message.

![one](one.jpg)

When we do a quick search on the response codes:

`220: SMTP Response code -> <SERVER> Service ready`
`250 – This SMTP server response simply means everything went well and your message was delivered to the recipient server.`

When we click submit, we can see two parameters being passed to /server.php, Name and Cmd=”STARTTLS”

STARTTLS is an email protocol command that tells an email server that an email client, including an email client running in a web browser, wants to turn an existing insecure connection into a secure one.

This directs us to mail service using SMTP protocol

SENDER_MAIL_SERVER  -------  TCP Handshake  ----->   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  <-------  220 Ready  -------   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  -------  EHLO  ------->   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  <-------  250 STARTTLS  ------   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  -------  STARTTLS  ------->   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  <------  220 GO AHEAD  ------   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  -------  TLS negotiation  ------>   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  -------  Encrypted mail  ------->   RECEIVER_MAIL_SERVER



## Starttls Downgrade attack


So in /server.php, we are getting the encrypted text of what the admin sent. Then comes tls downgrade attacks prevent email encryption or STARTTLS downgrade attacks. SMTP client as part of the protocol negotiation. If the client supports encryption (TLS), it will understand the STARTTLS verb and will initiate a TLS exchange before sending the email to ensure it is encrypted. If the client doesn't know TLS, it will simply ignore the STARTTLS and send the email in clear. 

SENDER_MAIL_SERVER  -------  TCP Handshake  ----->   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  <-------  220 Ready  -------   RECEIVER_MAIL_SERVER
SENDER_MAIL_SERVER  -------  EHLO  ------->   RECEIVER_MAIL_SERVER
SENDER  <------ XXXXXXXX ------ MITM ------ 250 STARTTLS ------  RECEIVER
SENDER_MAIL_SERVER  -----  Email in Clear text  ----->   RECEIVER_MAIL_SERVER

A downgrade attack replaces STARTTLS with a garbage string such as XXXXXXXX. The client sees the garbage string, and it may decide it is an option it doesn't know and so happily falls back to sending the email in clear text. The attacker usually replaces the command with garbage rather than stripping it out because this preserves the packet size and therefore, makes it easier. The eight letters in the garbage string in the option command allow us to detect that a TLS downgrade attack has been carried out and we can measure its prevalence. 

![two](two.jpg)

When we give junk values as Cmd, we can not that:
Numbers and printable characters are blocked, so we inject unicode values with length = 8 to do the downgrade and receive the message in clear form which says key is `sT4yH0M3`
Now where to input the key? In the source code of /server.php, we see a hint `go to /final.php`

In the /final.php, key is asked for and when we submit the key(?key=sT4yH0M3), we receive the first phase which is a strcasecmp bypass which can be bypassed by giving an array value.  strcasecmp had some issues when comparing a string to something else.
If I set $_GET['abc'] equal to an empty array, then strcmp would return a NULL. Due to some inherent weaknesses in PHP's comparisons, NULL == 0 will return true. Giving `?abc[]=some_junk_value`, we get the next phase. 
 
## Deserialization to RCE 
 
PHP serialization supports references. After the input field value of the unserialized object is unserialized, it can be matched with the covered flag value. Create and serialize data with a reference variable set for flag field in input.
R-> pointer reference
stdClass-> PHP's generic empty class
When we give the input `?def=O:8:"stdClass":2:{s:4:"flag";N;s:3:"xyz";R:2;}` we get the last phase which is deserialisation to RCE. 
After going into the functions Dogs and then Cats, a  system function is called on the input. Calling both functions along with giving a system command to list out the files in the service by `?ghi=O:4:"Dogs":1:{s:3:"obj";O:4:"Cats":1:{s:3:"cmd";s:2:"ls";}}`
We receive the final flag variable  inside the file running by reading the /final.php using `?ghi=O:4:"Dogs":1:{s:3:"obj";O:4:"Cats":1:{s:3:"cmd";s:13:"cat%20final.php";}}`
 
![three](three.jpg)

## Flag

**FLAG**: `inctf{Rc3_n_d0wngr4d35_d0n7_g0_W3Ll:}`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.

