# grep-db

## Consider the world’s simplest database, implemented as two Bash functions:

```sh
#!/bin/bash

db_set () {
  echo "$1,$2" >> database
}

db_get () {
  grep "^$1," database | sed -e "s/^$1,//" | tail -n 1
}
```

- These two functions implement a key-value store. 
- You can call db_set key value, which will store key and value in the database. 
- The key and value can be (almost) anything you like—for example, the value could be a JSON document. 
- You can then call db_get key, which looks up the most recent value associated with that particular key and returns it.

And it works:
```
$ db_set 123456 '{"name":"London","attractions":["Big Ben","London Eye"]}'
$ db_set 42 '{"name":"San Francisco","attractions":["Golden Gate Bridge"]}'

$ db_get 42
{"name":"San Francisco","attractions":["Golden Gate Bridge"]}
```

- The underlying storage format is very simple: a text file where each line contains a key-value pair, separated by a comma (roughly like a CSV file, ignoring escaping issues). 
- Every call to db_set appends to the end of the file, so if you update a key several times, the old versions of the value are not overwritten—you need to look at the last occurrence of a key in a file to find the latest value (hence the `tail -n 1` in `db_get`):

```
$ db_set 42 '{"name":"San Francisco","attractions":["Exploratorium"]}'

$ db_get 42
{"name":"San Francisco","attractions":["Exploratorium"]}
```

```
$ cat database
123456,{"name":"London","attractions":["Big Ben","London Eye"]}
42,{"name":"San Francisco","attractions":["Golden Gate Bridge"]}
42,{"name":"San Francisco","attractions":["Exploratorium"]}
```

## Steps to run
```
$ chmod +x kvdb.sh
$ source ./kvdb.sh

Now you can directly use the functions in terminal:
$ db_set 123 '{"name":"Test","value":42}'
$ db_get 123
```

## References
- https://www.linkedin.com/feed/update/urn:li:activity:7340723046271385601/
- [Why grep is Fast: NFA, DFA, and Regex Simplified](https://sahilserver.substack.com/p/the-magic-behind-grep)