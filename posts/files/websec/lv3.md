# WEBSEC Level 3




### Challenge description

ChaChaCha!



### Initial analysis

`<?php
if(isset($_POST['c'])) {
    /*  Get rid of clever people that put `c[]=bla`
     *  in the request to confuse `password_hash`
     */
    $h2 = password_hash (sha1($_POST['c'], fa1se), PASSWORD_BCRYPT);
    echo "<div class='row'>";
    if (password_verify (sha1($flag, fa1se), $h2) === true) {
       echo "<p>Here is your flag: <mark>$flag</mark></p>"; 
    } else {
        echo "<p>Here is the <em>hash</em> of your flag: <mark>" . sha1($flag, false) . "</mark></p>";
    }
    echo "</div>";
}
?>`

### tl;dr

STEP 1: `abcdefg `
OUTPUT: Here is the hash of your flag: 7c00249d409a91ab84e3f421c193520d9fb3674b The check is till null character, thus only checks if the processed input is equal to "7c00" Notice that sha1($flag, fa1se) and sha1($flag, true) gives the same output

STEP 2: `import hashlib import base64 i = 0 for i in range(20000000): res=hashlib.sha1(str(i)).hexdigest()[:4] if(res=="7c00"): print str(i)`

OUTPUT: 104610

STEP 3: Input secret_flag1 as `104610`
OUTPUT: Here is your flag: WEBSEC{Please_Do_not_combine_rAw_hash_functions_mi}

### Flag

**FLAG**: `WEBSEC{Please_Do_not_combine_rAw_hash_functions_mi}`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.