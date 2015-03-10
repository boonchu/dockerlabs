###### Getting started with Docker
* [Starting with docker service](https://access.redhat.com/articles/881893)
* [Using Vagrant to deploy docker](https://github.com/boonchu/dockerlabs/tree/master/Vagrant)
* Developing code with docker

###### Setup [Private Docker Registry](https://github.com/docker/docker-registry/blob/master/README.md)
* CentOS 7 come with Docker registry. Registry listens to port 5000.
```
$ sudo systemctl status docker-registry
docker-registry.service - Registry server for Docker
   Loaded: loaded (/usr/lib/systemd/system/docker-registry.service; enabled)
   Active: active (running) since Mon 2015-03-09 08:00:00 PDT; 11min ago
 Main PID: 627 (gunicorn)
   CGroup: /system.slice/docker-registry.service
           ├─627 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
           ├─778 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
           ├─798 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
           ├─801 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
           ├─802 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
           ├─805 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
           ├─818 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
           ├─819 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
           └─822 /usr/bin/python /usr/bin/gunicorn --access-logfile - --debug --max-requests 100 --graceful-timeout 3600 -t 3600 -k gevent -b 0.0.0.0:5000 ...
```
* configuration saved at /etc/docker-registry.yml YAML file.
```
> tail -3 /etc/docker-registry.yml
local:
    storage: local
    storage_path: /var/lib/docker-registry
```
* images location 
```
> ls -lZ /var/lib/docker-registry/
drwxr-xr-x. root root system_u:object_r:var_lib_t:s0   images
drwxr-xr-x. root root system_u:object_r:var_lib_t:s0   repositories
```
###### Using [Red Hat Enterprise 7.x with Docker](https://github.com/boonchu/dockerlabs/blob/master/RHEL7-docker.md)
###### docker for DevOps
* start docker container
```
$ sudo docker start python_web
python_web
```
* stop docker container
```
$ sudo docker stop python_web
python_web

$ sudo docker ps -l
CONTAINER ID        IMAGE                                     COMMAND                CREATED             STATUS                      PORTS               NAMES
3a75faa81424        rhel-server-docker-7.0-23.x86_64:latest   "/bin/python -m Simp   About an hour ago   Exited (-1) 4 seconds ago                       python_web
```
* kill docker container
```
$ sudo docker kill --signal="SIGKILL" python_web
python_web

$ curl http://localhost:8000
curl: (7) Failed connect to localhost:8000; Connection refused

$ sudo docker ps -l
CONTAINER ID        IMAGE                                     COMMAND                CREATED             STATUS                      PORTS               NAMES
3a75faa81424        rhel-server-docker-7.0-23.x86_64:latest   "/bin/python -m Simp   About an hour ago   Exited (-1) 6 seconds ago                       python_web
```
* how to remove docker container
```
$ sudo docker stop python_web
python_web

$ sudo docker rm python_web
python_web
```
* how to enter into an active container
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
###### Building Docker images [manual method]
* clean everything to save some spaces and inactive images
```
$ sudo docker rm $(sudo docker ps -a -q)
$ sudo docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
* ensure to have clean container. test new image with apache httpd and remove it.
```
$ sudo docker run --name="t_httpd" -v /etc/yum.repos.d:/etc/yum.repos.d \
   -i --rm server1.cracker.org:5000/myrhel7.0 /bin/bash \
   -c "yum clean all; yum update -y -nogpgcheck; \
   yum install --nogpgcheck -y httpd; yum clean all"
   
```
* check the image status and commit it
```
$ sudo docker ps -l
CONTAINER ID        IMAGE                                     COMMAND                CREATED              STATUS                      PORTS               NAMES
171e49e42ea8        rhel-server-docker-7.0-23.x86_64:latest   "/bin/bash -c 'yum c   About a minute ago   Exited (0) 34 seconds ago                       t_httpd
[11:26 Mon Mar 09] ~

$ sudo docker commit -m "t_httpd_chg1" -a "Boonchu Ngam" 171e49e42ea8 rhel_httpd
ed2decab231e11b6691519f5b440dffcd49f8a5a6be9f0abaddbab5416342be6

$ sudo docker images
REPOSITORY                           TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
rhel_httpd                           latest              ed2decab231e        11 seconds ago      177.2 MB
server1.cracker.org:5000/myrhel6.5   latest              8dc6a04270df        9 months ago        151.5 MB
rhel-server-docker-6.5-12.x86_64     latest              8dc6a04270df        9 months ago        151.5 MB
rhel-server-docker-7.0-23.x86_64     latest              bef54b8f8a2f        9 months ago        139.6 MB
server1.cracker.org:5000/myrhel7.0   latest              bef54b8f8a2f        9 months ago        139.6 MB
```
* run the httpd container
```
$ sudo docker run -p 8080:80 --rm -i rhel_httpd:latest /usr/sbin/httpd -DFOREGROUND
```
* you should see Cent OS default home page

###### Building Docker images [using Dockerfile]
* build own Dockerfile
```
$ cat Dockerfile
FROM server1.cracker.org:5000/myrhel7.0

ADD ks.cracker.org_Kickstart_RHEL7_rhel7.1-beta-core.repo /etc/yum.repos.d/

RUN yum repolist all
RUN yum update -y --nogpgcheck
RUN yum install httpd -y --nogpgcheck
RUN echo "container.example.com" > /etc/hostname

RUN bash -c 'echo "Your web server test is successful" >> /var/www/html/index.html'
```
* build image
```
$ sudo docker build -t rhel_httpd .
Sending build context to Docker daemon 3.584 kB
Sending build context to Docker daemon
Step 0 : FROM server1.cracker.org:5000/myrhel7.0
 ---> bef54b8f8a2f
Step 1 : ADD ks.cracker.org_Kickstart_RHEL7_rhel7.1-beta-core.repo /etc/yum.repos.d/
 ---> Using cache
 ---> 48432d46194e
Step 2 : RUN yum repolist all
 ---> Using cache
 ---> 7ad81509dd32
Step 3 : RUN yum update -y --nogpgcheck
 ---> Using cache
 ---> 5eded81efaac
Step 4 : RUN yum install httpd -y --nogpgcheck
 ---> Using cache
 ---> 142110dc45f9
Step 5 : RUN echo "container.example.com" > /etc/hostname
 ---> Using cache
 ---> 1e242a27de4a
Step 6 : RUN bash -c 'echo "Your web server test is successful" >> /var/www/html/index.html'
 ---> Using cache
 ---> d9f0e831ad0e
Successfully built d9f0e831ad0e
```
* ensure all untagged and stopped container not leaving on host
```
$ sudo docker rm $(sudo docker ps -a -q)
$ sudo docker rmi $(sudo docker images -q --filter "dangling=true")
```
* run the image again
```
$ sudo docker run -p 8080:80 --rm -i rhel_httpd:latest /usr/sbin/httpd -DFOREGROUND
```
* you should see custom message. If you can notice the different result from the same image while I manually made the image change.
```
$ curl vmk2:8080
Your web server test is successful
```
