# MongoDB Installation Steps for Production Environment on Redhat Linux 


### Prerequisite:
MongoDB <a href="https://docs.mongodb.org/manual/release-notes/3.0-compatibility/#compatibility-platform-support"> no longer supports</a> 32-bit x86 platforms.

Before installation we need to make changes in OS level.

### Step 1. Transparent Huge Pages
### Step 2. Linux Ulimit
### Step 3. Virtual Memory
### Step 4. Swappiness
### Step 5. Network Stack
### Step 6. Security Enhanced Linux

## 1. Transparent Huge Pages
Disable THP by saving the following file to /etc/init.d/disable-transparent-hugepages.
Transparent Huge Pages (THP) is a Linux memory management system that reduces the overhead of Translation Lookaside Buffer (TLB) lookups on machines with large amounts of memory by using larger memory pages.
```console
#vim  /etc/init.d/disable-transparent-hugepages
```
```console
#!/bin/bash
### BEGIN INIT INFO
# Provides:          disable-transparent-hugepages
# Required-Start:    $local_fs
# Required-Stop:
# X-Start-Before:    mongod mongodb-mms-automation-agent
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Disable Linux transparent huge pages
# Description:       Disable Linux transparent huge pages, to improve
#                    database performance.
### END INIT INFO
```
```shell
case $1 in
  start)
    if [ -d /sys/kernel/mm/transparent_hugepage ]; then
      thp_path=/sys/kernel/mm/transparent_hugepage
    elif [ -d /sys/kernel/mm/redhat_transparent_hugepage ]; then
      thp_path=/sys/kernel/mm/redhat_transparent_hugepage
    else
      return 0
    fi
  
    echo 'never' > ${thp_path}/enabled
    echo 'never' > ${thp_path}/defrag
  
    re='^[0-1]+$'
    if [[ $(cat ${thp_path}/khugepaged/defrag) =~ $re ]]
    then
      # RHEL 7
      echo 0  > ${thp_path}/khugepaged/defrag
    else
      # RHEL 6
      echo 'no' > ${thp_path}/khugepaged/defrag
    fi
  
    unset re
    unset thp_path
    ;;
esac
```
Copy and paste it in the above path “**/etc/init.d/disable-transparent-hugepages**”.

```console
#chmod 755 /etc/init.d/disable-transparent-hugepages
#update-rc.d disable-transparent-hugepages defaults
```
**Note**: Repeat on each node with above steps and reboot to enable the changes.

## 2. Linux Ulimit
The value of ulimit is one of the mechanisms used by Unix OSs such as Linux to prevent a single user from using too many system resources, such as files, threads, network connections, etc.
```console
#vim /etc/security/limits.conf
```
![Picture1](https://storage.googleapis.com/slt12/Picture1.png)
**Note:** This change only applies to new shells, meaning you must restart mongodb.

Recommended values for MongoDB:
* -f (file size): unlimited
* -t (cpu time): unlimited
* -v (virtual memory): unlimited [1]
* -l (locked-in-memory size): unlimited
* -n (open files): 64000
* -m (memory size): unlimited [1] [2]
* -u (processes/threads): 64000

## 3. Virtual Memory
  Make the changes in sysctl configuration file for Virtual memory.
```console
#vim /etc/sysctl.conf

vm.dirty_ratio=15
vm.dirty_background_ratio=5
```

## 4. Swapping Memory
   Below configuration file with valid parameter values.
```console
#vim /etc/sysctl.conf

vm.swappiness=1
```
**Note:** you must run the command “/sbin/sysctl -p” as root/sudo (or reboot) to apply the changes.

## 5. Network Stack
Please make sure do  the network changes in the below configuration file.
```console
#vim /etc/sysctl.conf
```
* net.core.somaxconn=4096
* net.ipv4.tcp_fin_timeout=30
* net.ipv4.tcp_keepalive_intvl=30
* net.ipv4.tcp_keepalive_time=120
* net.ipv4.tcp_max_syn_backlog=4096

After making the changes please save it and run the command from root user:
```sh
# /sbin/sysctl -p
```
## 6.Security Enhanced Linux
```console
#vim /etc/sysconfig/selinux
```
SELINUX=disabled
# Installation & Configuration
## Step 1: Configure the package management system (yum)

Create a /etc/yum.repos.d/mongodb-org-3.6.repo file using the  below repository file.
Open file with vim editor and paste into the file as shown below:
```console
Ex:#vim /etc/yum.repos.d/mongodb-org-3.6.repo

[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```
Step 2: Install MongoDB Packages
```console
#sudo yum install -y mongodb-org  (Install latest)
```
Or
```console
#sudo yum install -y mongodb-org-3.6.6 mongodb-org-server-3.6.6 mongodb-org-shell-3.6.6 mongodb-org-mongos-3.6.6 mongodb-org-tools-3.6.6  (Install specific release)
```
After installation please check with below commands:
```console
#sudo service mongod start  (To start the mongodb)
#sudo chkconfig mongod on (Startup up at boot time)
#sudo service mongod stop  (Stop the services)
#sudo service mongod restart (Restart the services)
#sudo service mongod status (To check the status)
```
## Step 3: Next, We will disable SELinux by editing the configuration file with vim
```console
#vim /etc/sysconfig/selinux
```
SELINUX=disabled

Change value 'enforcing' to 'disabled',then save and reboot the server.

## Step 4: Configure Firewalld

In this step, we already disabled SELinux. For security reasons, we will now enable firewalld on all nodes and open only the ports that are used by MongoDB and SSH.
```console
#yum -y install firewalld
```
Start firewalld and enable it to start at boot time.
```console
#systemctl start firewalld
#systemctl enable firewalld
```
Next, open your ssh port and the MongoDB default port 27017.
```console
#firewall-cmd --permanent --add-port=22/tcp
#firewall-cmd --permanent --add-port=27017/tcp
```
Reload the firewall to apply the changes
```console
#firewall-cmd --reload
```

## Step 5: Configure MongoDB Replica Set

Edit the MongoDB configuration file to enable authentication and change the security setting then save and quit.
```console
#sudo vim /etc/mongod.conf
```
security:
  authorization: enabled

## Step 6: Connect to MongoDB Shell and  create a admin user for use in the database
```console
#sudo service mongod start

#mongo

>db.createUser({user: "admin", pwd: "admin123", roles:[{role: "root", db: "admin"}]})
>quit()
```

## Step 7: Connect with admin user was created successfully:
```console
#mongo -u admin -p --authenticationDatabase admin
```

## Step 8: Creating the Cluster

1.First step is to tell mongodb the name of the replica set and stop the services.and then update the replication section of the config file with below command:
```console
#sudo service mongod stop
#vim /etc/mongod.conf

replication:
  replSetName: rs0
```
2.Start  mongodb services
```console
#sudo service mongod start
```
Note:Repeat the above steps for other nodes.

3.Next step is to create a shared keyfile the servers can use to authenticate the connection between them.
```console
#sudo openssl rand -base64 756 > /etc/ssl/mongodb-internal.key
#sudo chown mongod:mongod /etc/ssl/mongodb-internal.key
#chmod 400 /etc/ssl/mongodb-internal.key
```
Then copy this file to the other nodes, into **/etc/ssl/mongodb-internal.key**
Update the MongoDB config to use this key file, change the security section as follows:

4.
```console
#vim /etc/mongod.conf
security:
  authorization: enabled
  keyFile: /etc/ssl/mongointernal.key
```

## Step 9: Next create replica set and run the mongodb from command line and register the nodes:
```console
#mongo -u admin -p --authenticationDatabase admin
```
```js
>rs.initiate(
  {
    _id : rs0,
    members: [
      { _id : 0, host : "<node1>:27017" },
      { _id : 1, host : "<node2>:27017" },
      { _id : 2, host : "<node3>:27017" }
    ]
  }
)


>rs.status()
```

### Output:
![Picture7](https://storage.googleapis.com/slt12/Picture7.png)

```js
>rs.isMaster()
```

## Output:
![Picture6](https://storage.googleapis.com/slt12/Picture6.png)

## Step 10: Test the Replication

In this example,taking as node1 as primary and node2,node3 as secondary nodes.

1.Login to the 'node1' server and open mongo shell.

Then create a new database 'lemp' and new 'stack' collection for the database.
```console
#ssh root@node1
#mongo
```
Then create a new database 'lemp' and new 'stack' collection for the database.
```console
>use lemp
>db.stack.save(
{
    "desc": "LEMP Stack",
    "apps":  ["Linux", "Nginx", "MySQL", "PHP"],
})
```
### Output:
![Picture4](https://storage.googleapis.com/slt12/Picture4.png)

2.Next, go to the 'SECONDARY' node 'node2' and open the mongo shell.
```console
#ssh root@node2
#mongo
```
Enable reading from the 'SECONDARY' node with the query 'rs.slaveOk()', and then check if the 'lemp' database exists on the 'SECONDARY' nodes.
```js
>rs.slaveOk()
>show dbs
>use lemp
>show collections
>db.stack.find()
```

### Output:
![Picture5](https://storage.googleapis.com/slt12/Picture5.png)
