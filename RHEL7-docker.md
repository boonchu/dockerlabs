###### Noted from RHEL7 docker container setup
* turn off firewalld and start docker service
```
$ sudo systemctl stop firewalld && sudo systemctl disable firewalld
$ sudo systemctl start docker.service && sudo systemctl enable docker.service
```
* recommend to enable selinux security on docker host
```
$ getenforce
Enforcing
```
* setup remote docker resgistry (recommended nginx for proxies and nfs for storage)
```
TBD
```
* deploys docker images to remote docker registry
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
* pushes docker images to remote docker registry
```
- change the option on /etc/sysconfig/docker if you see error with unknown CA cert https issue while you push it.
OPTIONS=--selinux-enabled -H fd:// --insecure-registry server1.cracker.org:5000

- restart docker service

- pushes the image
$ sudo docker push server1.cracker.org:5000/myrhel7.0
The push refers to a repository [server1.cracker.org:5000/myrhel7.0] (len: 1)
Sending image list
Pushing repository server1.cracker.org:5000/myrhel7.0 (1 tags)
Image bef54b8f8a2f already pushed, skipping
Pushing tag for rev [bef54b8f8a2f] on {http://server1.cracker.org:5000/v1/repositories/myrhel7.0/tags/latest}

$ sudo docker push server1.cracker.org:5000/myrhel6.5
The push refers to a repository [server1.cracker.org:5000/myrhel6.5] (len: 1)
Sending image list
Pushing repository server1.cracker.org:5000/myrhel6.5 (1 tags)
8dc6a04270df: Image successfully pushed
Pushing tag for rev [8dc6a04270df] on {http://server1.cracker.org:5000/v1/repositories/myrhel6.5/tags/latest}
```
* noted to follow the [instruction to install cert file](https://github.com/docker/docker/issues/9118) if you have valid cert.
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
* example 2: simple http service python2.x module
```
- create new index.html file
bigchoo@vmk2 1012 $ cat /var/www/html/index.html
<html>
  <body>
        <h1>hello world</h1>
  </body>
</html>

- run docker from port 8000
bigchoo@vmk2 1008 $ sudo docker run -d -p 8000:8000 --name="python_web"  -v /usr/sbin:/usr/sbin -v /usr/bin:/usr/bin -v /usr/lib64:/usr/lib64   -w /var/www/html -v /var/www/html:/var/www/html server1.cracker.org:5000/myrhel7.0  /bin/python -m SimpleHTTPServer 8000
3a75faa814243ec6dddbed5ab42b6412663735a0e08622b7c41dfb29499422ae

- check docker running process
bigchoo@vmk2 1009 $ sudo docker ps -a
CONTAINER ID        IMAGE                                     COMMAND                CREATED             STATUS              PORTS                    NAMES
3a75faa81424        rhel-server-docker-7.0-23.x86_64:latest   "/bin/python -m Simp   10 seconds ago      Up 9 seconds        0.0.0.0:8000->8000/tcp   python_web

- check service listener port
bigchoo@vmk2 1010 $ sudo lsof -i :8000
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
docker  2783 root    4u  IPv6  22086      0t0  TCP *:irdmi (LISTEN)

- curl for testing
bigchoo@vmk2 1011 $ curl localhost:8000
<html>
  <body>
        <h1>hello world</h1>
  </body>
</html>

```
* example 3: logger service
```
- run docker and dump message via logger 
bigchoo@vmk2 1014 $ sudo docker run --name="log_test" -v /dev/log:/dev/log --rm server1.cracker.org:5000/myrhel7.0 logger "Testing from docker container"

- check messages from journalctl
bigchoo@vmk2 1015 $ sudo journalctl -b | grep Testing
Mar 09 09:09:01 vmk2.cracker.org logger[2875]: Testing from docker container
```
* example 4: using inspect to return the private IP address on container end
```
bigchoo@vmk2 1018 $ sudo docker inspect --format='{{.NetworkSettings.IPAddress}}' python_web
172.17.0.2
```
* example 5: using inspect to return the container port binding info
```
bigchoo@vmk2 1019 $ sudo docker inspect --format='{{.HostConfig.PortBindings}}' python_web
map[8000/tcp:[map[HostIp: HostPort:8000]]]
```
###### docker for DevOps
* start docker container
```
$ sudo docker start python_web
python_web
```
* how to enter to active container
```
- get active State PID from container name 
bigchoo@vmk2 1011 $ sudo docker inspect --format='{{.State.Pid}}' python_web
2715

- enter the container pyhton_web
bigchoo@vmk2 1013 $ sudo nsenter -m -u -n -i -p -t 2715 /bin/bash

- investigate the container
root@3a75faa81424 2 $ free -m
              total        used        free      shared  buff/cache   available
Mem:           1840         215        1353           8         271        1472
Swap:          1535           0        1535

root@3a75faa81424 3 $ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
4: eth0: <BROADCAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.2/16 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:acff:fe11:2/64 scope link
       valid_lft forever preferred_lft forever
```
