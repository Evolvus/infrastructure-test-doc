

Nginx [engine x] is free and open source high-performance web server. It also acts as a reverse proxy server, as well as. This page shows how to install Nginx server on a CentOS 7 or RHEL 7 and configure a static web site.

The procedure to install Nginx weber server on an RHEL 7 or CentOS Linux 7 is as follows:

Login to your cloud server or bare metal server using ssh command:br/>
```console
ssh user@cloud-server-ip
```
Create the file named /etc/yum.repos.d/nginx.repo using a text editor such as vim command
```console
sudo vi /etc/yum.repos.d/nginx.repo
```
Install nginx package using the yum command:
```console
sudo yum update
sudo yum install nginx
```
Let us see all steps, commands and configuration in details.

### Step 1: Configure Nginx
Create or edit file using vim/vi/joe text editor:
```console
$ vi /etc/yum.repos.d/nginx.repo
```
Append following for RHEL (Red Hat Enterprise Linux) version 7.x:
```console
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/rhel/7/$basearch/
gpgcheck=0
enabled=1
```
Save and close the file in vi.

###Step 2: Install Nginx on RHEL 7
Type the following yum command on your RHEL 7.x server:
```console
$ sudo yum install nginx
```
### Step 3 – Start/stop/restart nginx server
First enable nginx service by running systemctl command so that it start at server boot time:
$ sudo systemctl enable nginx

Sample outputs:
```console
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
```
### Start Nginx command
```console
$ sudo systemctl start nginx
```
### Stop Nginx command
```console
$ sudo systemctl stop nginx
```
### Restart Nginx command
```console
$ sudo systemctl restart nginx
```
### Find status of Nginx server command
```console
$ sudo systemctl status nginx
```
### Sample outputs:
```console
* nginx.service - nginx - high performance web server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2018-01-18 21:45:54 UTC; 8s ago
     Docs: http://nginx.org/en/docs/
  Process: 656 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0/SUCCESS)
 Main PID: 657 (nginx)
   CGroup: /system.slice/nginx.service
           ??657 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
           ??658 nginx: worker process
 
Jan 18 21:45:54 cenots7-theosin systemd[1]: Starting nginx - high performance web server...
Jan 18 21:45:54 cenots7-theosin systemd[1]: Started nginx - high performance web server.

### Step 4 – Open port 80 and 443 using firewall-cmd
You must open and enable port 80 and 443 using the firewall-cmd command:
```console
$ sudo firewall-cmd --permanent --zone=public --add-service=http
$ sudo firewall-cmd --permanent --zone=public --add-service=https
$ sudo firewall-cmd --reload
```
![Picture19](https://storage.googleapis.com/slt12/Picture19.PNG)

### Step 5 – Test it
Verify that port 80 or 443 opened using ss command:
```console
$ sudo ss -tulpn
```
### Sample outputs:

Netid State      Recv-Q Send-Q         Local Address:Port                        Peer Address:Port              
udp   UNCONN     0      0                          *:60139                                  *:*                   users:(("dhclient",pid=242,fd=20))
udp   UNCONN     0      0                          *:68                                     *:*                   users:(("dhclient",pid=242,fd=6))
udp   UNCONN     0      0                         :::34050                                 :::*                   users:(("dhclient",pid=242,fd=21))
tcp   LISTEN     0      128                        *:80                                     *:*                   users:(("nginx",pid=696,fd=6),("nginx",pid=695,fd=6))

If you do not know your server IP address run the following ip command:
```console
$ ip a
```
### Sample outputs:
```console
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
4: eth0@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP qlen 1000
    link/ether 00:16:3e:ac:ba:1e brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.21.136.134/24 brd 10.21.136.255 scope global dynamic eth0
       valid_lft 3348sec preferred_lft 3348sec
    inet6 fe80::216:3eff:feac:ba1e/64 scope link 
       valid_lft forever preferred_lft forever
```
So my IP address is 10.21.136.134. Fire a web browser and type the ip address:
```console
http://10.21.136.134
```
![Picture18](https://storage.googleapis.com/slt12/Picture18.PNG)

You can also use the curl command to get same info using the cli:
```console
$ curl -I http://10.21.136.13
$ curl http://10.21.136.13
```
![Picture17](https://storage.googleapis.com/slt12/Picture17.PNG)

### Step 6 – Configure Nginx server

Config dir – /etc/nginx/
Master/Global config file – /etc/nginx/nginx.conf
Port 80 http config file – /etc/nginx/conf.d/default
TCP ports opened by Nginx – 80 (HTTP), 443 (HTTPS)
Document root directory – /usr/share/nginx/html

To edit files use a text editor such as vi
```console
$ sudo vi /etc/nginx/conf.d/default
```
You can upload or copy your html/css/js and images to /usr/share/nginx/html/
```console
$ cd /usr/share/nginx/html/
$ sudo cp /backups/theos.in/*.html .
$ sudo cp /backups/theos.in/*.css .
```
Copy from local desktop to the remote server using the rsync command or rsync command:
```console
$ rsync ~/projects/static/theos.in/* root@10.21.136.134:/usr/share/nginx/html/
```