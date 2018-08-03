MongoDB Restore
## Introduction:

MongoDB is a cross-platform, document oriented database that provides, high performance, high availability, and easy scalability. MongoDB works on concept of collection and document.

## Database:

Database is a physical container for collections. Each database gets its own set of files on the file system. A single MongoDB server typically has multiple databases.

## Collection:
Collection is a group of MongoDB documents. It is the equivalent of an RDBMS table. A collection exists within a single database.

## Document:
A document is a set of key-value pairs. Documents have dynamic schema.

## Step 1:
Check mongo instance services root@evolvus-VirtualBox:~# service mongod status

### Output:
![Picture8](https://storage.googleapis.com/slt12/Picture8.png)

## Step 2:
Connect mongo instance

root@evolvus-VirtualBox:/home/evolvus# mongo --host 192.168.1.82:27017

### Output:
```console
MongoDB shell version v3.6.5

connecting to: mongodb://192.168.1.82:27017/
MongoDB server version: 3.6.5

>
```
Step 3: 
Check the database list
```console
>show dbs
```

### Output:
```console
admin 0.000GB 
config 0.000GB 
local 0.000GB 
sampledb 0.000GB 
testdb 0.000GB
```
## Step 4:
Insert the values test database
```consle
> db.test.insert({name:"xyz",age:29,location:"bangalore"})
```
### Output:
WriteResult({ "nInserted" : 1 })
```console
> db.test.insert({name:"abc",age:30,location:"hyderabad"})
```
### Output:
WriteResult({ "nInserted" : 1 })
```console
> db.test.insert({name:"ccc",age:38,location:"chennai"})
```
### Output:
WriteResult({ "nInserted" : 1 })

## Step 5:
List of document values into collection > db.test.find()
```js
{ "_id" : ObjectId("5b35e20584ea820bee5e21ad"), "name" : "xyz", "age" : 29, "location" : "bangalore" }

{ "_id" : ObjectId("5b35e21684ea820bee5e21ae"), "name" : "abc", "age" : 30, "location" : "hyderabad" }

{ "_id" : ObjectId("5b35e22b84ea820bee5e21af"), "name" : "ccc", "age" : 38, "location" : "chennai"
}

{ "_id" : ObjectId("5b35f93b84ea820bee5e21b0"), "name" : "abcd", "age" : 38, "location" : "Mumbai" }
```

## Step 6:
Take the backup of respective database
```console
root@evolvus-VirtualBox:/var/lib/mongodb# mongodump -h localhost --port 27017 -d test -o /var/lib/mongodb
```
### Output:
```console
2018-06-29T14:59:21.528+0530 writing test.test to 2018-06-29T14:59:21.531+0530 done dumping test.test (4 documents)
```
## Step 7:
Check the backup file whether it has been generated or not
```console
/var/lib/mongodb/test# ls -ltr
```
total 8
```bash
-rw-r--r-- 1 root root 123 Jun 29 14:59 test.metadata.json
-rw-r--r-- 1 root root 288 Jun 29 14:59 test.bson
```
```console
/var/lib/mongodb/test# mongorestore -h localhost --port 27017 -d test /var/lib/mongodb/test/
```
### Output:

2018-06-29T15:11:15.230+0530 the --db and --collection args should only be used when restoring from a BSON file. Other uses are deprecated and will not exist in the future; use --
```console
nsInclude instead		
2018-06-29T15:11:15.231+0530	building a list of collections to restore from
/var/lib/mongodb/test dir		
2018-06-29T15:11:15.233+0530	reading metadata for test.test from
/var/lib/mongodb/test/test.metadata.json
2018-06-29T15:11:15.310+0530	restoring test.test from /var/lib/mongodb/test/test.bson
2018-06-29T15:11:15.313+0530	no indexes to restore
2018-06-29T15:11:15.313+0530	finished restoring test.test (4 documents)	
2018-06-29T15:11:15.313+0530	done
```

## Step 9:
To check the count of objects after restoring the database
```console
/var/lib/mongodb/test# mongo test

>db.test.count()
4

>db.test.find()
```
### Output:
![Picture9](https://storage.googleapis.com/slt12/Picture9.png)

### To schedule the backup job:

1.Create shell script using mongodump utility 
```sh
vim backup.sh
#!/bin/sh
DIR=`date +%d%m%y` 
DEST=/var/lib/mongodb/$DIR 
mkdir $DEST
mongodump -h localhost -d test -o $DEST 
mongodump -h localhost -d testdb -o $DEST 
mongodump -h localhost -d admin -o $DEST 
zip -r $DEST.zip $DEST
```
Save & Quit

2.Give the Privilege and execute the script
```sh
/var/lib/mongodb# chmod +x backup.sh 
root@evolvus-VirtualBox:/var/lib/mongodb# ./backup.sh
```
### Output:
![Picture10](https://storage.googleapis.com/slt12/Picture10.png)

```console
/var/lib/mongodb# ls -ltr
```

### Output:	
```console				
drwxr-xr-x 5 root	root	4096 Jun 29 16:45	290618	
-rw-r--r-- 1 root	root	2640 Jun 29 16:45	290618.zip
```

### APPENDIX:

https://docs.mongodb.com/manual/administration/
