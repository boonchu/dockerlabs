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

