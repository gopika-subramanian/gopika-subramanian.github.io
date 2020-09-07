# Alles CTF 2020 OnlyFreights

**tl;dr**

+ Prototype pollution to RCE
+ Bypass the comparison check of binary
+ Execute the binary to read flags using code execution



## Challenge description

Check out my OnlyFreights! A website to classify all the freight ships.

NOTE: There is a secret cargo stored at /flag.txt,

but you need to convince the /guard executable to hand it to you!

Challenge Files: only-freights.zip

## Initial analysis
We are given with an application which can add container values, we are given the source code and we can see that there are files like server.js, app.js, guard.c etc.

![](/posts/files/allesctf_onlyfreights/one.png)

In the guard.c file, we notice the function `print_flag()` which is called when `is_interactive()` function returns true when the input given is equal to the sum of two randomly generated number. 

Next thing we notice is the hint `ensure RCE is needed to get the flag` in the Dockerfile and `you need to convince the /guard executable` in the description. So this gives us the walkthrough of getting RCE to execute the guard binary to get the flag. 

## Step 1 -> RCE 
In the server.js, we see `const { spawn } = require('child_process');` and we have three endpoints and one with the comment, supposedly another hint `TODO: remove before release` in the /_debug/stats endpoint, which has a spawn() function. child_process.spawn() has a set of options like env, shell, serialization etc and the note in the node.js documentation says `If the shell option is enabled, do not pass unsanitized user input to this function. Any input containing shell metacharacters may be used to trigger arbitrary command execution.` 

The next thing we notice is ` find(parentPath)[id] = value;` allows us to write to any arbitrary property, even __proto__ which leads us to prototype pollution. Exploiting prototype pollution – RCE in Kibana `(CVE-2019-7609)` was an example of escating to Code execution from prototype pollution where they use use `NODE_OPTIONS` to pass command line arguments to `node` using the env variables. To make use of this NODE_OPTIONS as in CVE-2019-7639, we have to set shell to node.

![](/posts/files/allesctf_onlyfreights/two.png)

Next is to create an env so that we can invoke the command with this variable created by creating a env variable aaaa "aaaa":"console.log(123);//",
"NODE_OPTIONS": "--require /proc/self/environ"


![](/posts/files/allesctf_onlyfreights/three.png)


Next give the shell provoke command `__proto__.env.AAAA='require("child_process").exec("cmd");process.exit()//')` to get the shell execution check which prints out the files in the directory.

![](/posts/files/allesctf_onlyfreights/four.png)

## Step 2 -> Execute the binary
In this part we had to execute the binary and make the comparison of a+b which are two random numbers to be equal to c, so we had to read the two random numbers, sum it up and give the numbers as the input to bypass the comparison check. 

The trick is to read stdin and stdout, sum the stdin and submit to get the flag, I was only able to execute it locally which gives the fake flag. This challenge took a lot of time to solve but it was a really interesting challenge. I will update with a better solution soon <3 


**FLAG**: ``

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.
