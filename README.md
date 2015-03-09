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
