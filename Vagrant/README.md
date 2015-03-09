###### Start with simple docker on Vagrant

* git clone from repository
* install [vagrant](http://www.vagrantup.com/downloads.html)
* create vagrant instance and enter to vagrant
```
$ cd dockerlabs && vagrant up
$ vagrant ssh
Last login: Fri Mar  7 16:57:20 2014 from 10.0.2.2
[vagrant@localhost ~]$
```
* build docker image by Dockerfile inside vagrant
```
$ cd /vagrant
$ sudo docker build -t simple .
$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
simple              latest              35f569a95bf2        15 seconds ago      371.4 MB
centos              latest              88f9454e60dd        3 days ago          223.9 MB
```
* run docker image
```
$ sudo docker run -p 80:80 -v /vagrant:/vagrant -d simple
d75284b39dd67f1466965abffb2e4f3552c9c28e9cc38fa7187156d3a94c6ac2
$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                NAMES
d75284b39dd6        simple:latest       "/usr/sbin/apachectl   24 seconds ago      Up 23 seconds       0.0.0.0:80->80/tcp   cocky_stallman
$ sudo yum install -y elinks
$ elinks http://192.168.33.10
```
