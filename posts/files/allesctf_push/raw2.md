# Alles CTF Push


### Challenge description
Push


### tl;dr
+ The website uses Nginx 1.14.1
+ Nginx 1.13.9+ versions has HTTP/2 Server Push 
+ Command-Line Client nghttp pre‑emptively push resources including the flag

### Initial Analysis
We are provided with a simple website with just a marquee tag with the content "push". The service does not show any directories while run with dirbusters and no git/svg/bzr dumps.

![](/posts/files/allesctf_push/one.png)

When we check Wappalyzer, we see that `Nginx version 1.14.1` is used. NGINX 1.13.9, released on February 20, 2018, includes support for HTTP/2 server push. 

![](/posts/files/allesctf_push/two.png)

Server push allows a server to `pre‑emptively push resources` to a remote client, anticipating that the client may soon request those resources. We can verify HTTP/2 Server Push by either The developer tools in your web browser or A command‑line HTTP/2 client such as nghttp. When we use the nghttp tool on the service we are given the flag folder.

![](/posts/files/allesctf_push/three.png)

When we go to the directory, we get the flag `ALLES{http2_push_dashdash_force}` printed. 


### Flag

**FLAG**: `ALLES{http2_push_dashdash_force}`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.
