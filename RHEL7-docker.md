###### Noted from RHEL7
* turn off firewalld and start docker service
```
$ sudo systemctl stop firewalld && sudo systemctl disable firewalld
$ sudo systemctl start docker.service && sudo systemctl enable docker.service
```
* recommend to enable selinux security on docker host
```
$ getenforce
```
* deploys docker images to local docker registry
```
bigchoo@vmk2 1022 $ sudo docker load -i ./rhel-server-docker-7.0-23.x86_64.tar.gz
bigchoo@vmk2 1024 $ sudo docker tag bef54b8f8a2f server1.cracker.org:5000/myrhel7.0
bigchoo@vmk2 1025 $ sudo docker images
REPOSITORY                           TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
server1.cracker.org:5000/myrhel7.0   latest              bef54b8f8a2f        9 months ago        139.6 MB
rhel-server-docker-7.0-23.x86_64     latest              bef54b8f8a2f        9 months ago        139.6 MB
rhel-server-docker-6.5-12.x86_64     latest              8dc6a04270df        9 months ago        151.5 MB
server1.cracker.org:5000/myrhel6.5   latest              8dc6a04270df        9 months ago        151.5 MB
```
* after container runs the command, it shows the IP address 172.17.0.3/16 and other related to ethernet info.
```
bigchoo@vmk2 1026 $ sudo docker run -v /usr/sbin:/usr/sbin -i -t --rm server1.cracker.org:5000/myrhel7.0 /usr/sbin/ip addr show eth0
6: eth0: <NO-CARRIER,BROADCAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state DOWN qlen 1000
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.3/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:3/64 scope link tentative
       valid_lft forever preferred_lft forever
```
