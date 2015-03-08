###### Start with simple docker 

* git clone from repository
* install [vagrant](http://www.vagrantup.com/downloads.html)
* create vagrant instance and enter to vagrant
```
* cd dockerlabs && vagrant up
* vagrant ssh
```
* build docker image
```
cd /vagrant
docker build -t simple .
```
