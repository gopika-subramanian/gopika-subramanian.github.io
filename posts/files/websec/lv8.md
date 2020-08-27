# WEBSEC Level 8


### Challenge description
Bypassing Security Checks

### Initial analysis
```$uploadedFile = sprintf('%1$s/%2$s', '/uploads', sha1($_FILES['fileToUpload']['name']) . '.gif');
                    if (file_exists ($uploadedFile)) { unlink ($uploadedFile); }
                    if ($_FILES['fileToUpload']['size'] <= 50000) {
                        if (getimagesize ($_FILES['fileToUpload']['tmp_name']) !== false) {
                            if (exif_imagetype($_FILES['fileToUpload']['tmp_name']) === IMAGETYPE_GIF) {
                                move_uploaded_file ($_FILES['fileToUpload']['tmp_name'], $uploadedFile);
                                echo '<p class="lead">Dump of <a href="/level08' . $uploadedFile . '">'. htmlentities($_FILES['fileToUpload']['name'])
                                ```


### tl;dr

STEP 1: Choose a gif file to be uploaded 
STEP 2: Intercept in Burp Suite 
STEP 3: Keep the GIF magic number(`GIF87a`) 
STEP 4: Add a php code to show flag.txt source  `<?php show_source('flag.txt');?>`
STEP 5: GIF89a

OUTPUT: WEBSEC{BypassingImageChecksToRCE}


### Flag

**FLAG**: `WEBSEC{BypassingImageChecksToRCE}`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.