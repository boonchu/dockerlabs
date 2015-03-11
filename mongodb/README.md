###### Building Docker images [using Dockerfile]
* build own Dockerfile
```
FROM server1.cracker.org:5000/myrhel7.0
MAINTAINER Boonchu Ngampairoijpibul<bigchoo@gmail.com>

ADD ks.cracker.org_Kickstart_RHEL7_rhel7.1-beta-core.repo /etc/yum.repos.d/
ADD epel.repo /etc/yum.repos.d/

RUN yum repolist all
RUN yum update -y --nogpgcheck
RUN yum install mongodb-server -y --nogpgcheck

RUN mkdir -p /var/lib/mongodb && \
    touch /var/lib/mongodb/.keep && \
    chown -R mongodb.mongodb /var/lib/mongodb

ADD mongodb.conf /etc/mongodb.conf

VOLUME [ "/var/lib/mongodb" ]

EXPOSE 27017

USER mongodb
WORKDIR /var/lib/mongodb

ENTRYPOINT [ "/usr/bin/mongod", "--config", "/etc/mongodb.conf" ]
CMD [ "--quiet" ]
```
* build image
```
$ sudo docker build -t rhel_mongodb .
Step 7 : RUN mkdir -p /var/lib/mongodb &&     touch /var/lib/mongodb/.keep &&     chown -R mongodb.mongodb /var/lib/mongodb                          [0/1854]
 ---> Running in cc833425a0c9
 ---> 3f044da14b50
Removing intermediate container cc833425a0c9
Step 8 : ADD mongodb.conf /etc/mongodb.conf
 ---> fa65a785fcc0
Removing intermediate container 6b407740e9fd
Step 9 : VOLUME /var/lib/mongodb
 ---> Running in 52b29404f073
 ---> 3eef68e37a55
Removing intermediate container 52b29404f073
Step 10 : EXPOSE 27017
 ---> Running in 794673bff1c5
 ---> 2768d7a35a9e
Removing intermediate container 794673bff1c5
Step 11 : USER mongodb
 ---> Running in 37e3dfcc3573
 ---> cce2636ed3cc
Removing intermediate container 37e3dfcc3573
Step 12 : WORKDIR /var/lib/mongodb
 ---> Running in df513fec3852
 ---> e8a2e3489ddf
Removing intermediate container df513fec3852
Step 13 : ENTRYPOINT /usr/bin/mongod --config /etc/mongodb.conf
 ---> Running in db21d006954f
 ---> 320ba7d05201
Removing intermediate container db21d006954f
Step 14 : CMD --quiet
 ---> Running in 374dac1bf21b
 ---> bbcfe37a2ee6
Removing intermediate container 374dac1bf21b
Successfully built bbcfe37a2ee6
```
* ensure all untagged and stopped container not leaving on host
```
$ sudo docker rm $(sudo docker ps -a -q)
$ sudo docker rmi $(sudo docker images -q --filter "dangling=true")
```
* enter the image
```
$ sudo docker run -it --name mongodb -p 27017:27017 -v /vol/nfs/mongodb:/var/lib/mongodb" --entrypoint /bin/bash --rm rhel_mongodb
bash-4.2$ id
uid=184(mongodb) gid=999(mongodb) groups=999(mongodb)
bash-4.2$ ls -lta /var/lib/mongodb/
total 4
drwxr-x---.  2 mongodb mongodb   18 Mar 11 00:01 .
-rw-r--r--.  1 mongodb mongodb    0 Mar 10 23:30 .keep
```
* run the image again
```
$ sudo docker run -d -p 27017:27017 --name mongodb  -v /vol/nfs/mongodb:/var/lib/mongodb" rhel_mongodb
```
