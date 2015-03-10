FROM server1.cracker.org:5000/myrhel7.0

ADD ks.cracker.org_Kickstart_RHEL7_rhel7.1-beta-core.repo /etc/yum.repos.d/

RUN yum repolist all
RUN yum update -y --nogpgcheck
RUN yum install httpd -y --nogpgcheck
RUN echo "container.example.com" > /etc/hostname

RUN bash -c 'echo "Your web server test is successful" >> /var/www/html/index.html'
