To completely remove MongoDB from a system, you must remove the MongoDB applications themselves, the configuration files, and any directories containing data and logs. The following section guides you through the necessary steps.

### 1. Stop MongoDB
Stop the mongod process by issuing the following command:
```console
sudo service mongod stop
```
### 2. Remove Packages
Remove any MongoDB packages that you had previously installed.
```console
sudo yum erase $(rpm -qa | grep mongodb-org)
```
### 3. Remove Data Directories
Remove MongoDB databases and log files.
```console
sudo rm -r /var/log/mongodb
sudo rm -r /var/lib/mongo
```