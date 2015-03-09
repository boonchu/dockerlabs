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
