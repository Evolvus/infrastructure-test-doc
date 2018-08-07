### 1. Stop Nginx service and remove Nginx auto start script :
```console
[root@rhel7 ~]# sudo systemctl stop nginx.service
[root@rhel7 ~]# sudo systemctl disable nginx.service
```
### 2. Remove Nginx user and it related directory :
```console
[root@rhel7 ~]# sudo userdel -r nginx
```
### 3. Delete and related Nginx installation directory :
```console
[root@rhel7 ~]# sudo rm -rf /etc/nginx
[root@rhel7 ~]# sudo rm -rf /var/log/nginx
[root@rhel7 ~]# sudo rm -rf /var/cache/nginx/
```
### 4. Remove the created nginx.service script under systemd :
```console
[root@rhel7 ~]# sudo rm -rf /usr/lib/systemd/system/nginx.service
```
