###### Start with simple docker 

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
