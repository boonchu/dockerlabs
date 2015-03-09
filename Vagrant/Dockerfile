FROM centos
MAINTAINER bigchoo <bigchoo@gmail.com>

#
# httpd
RUN yum -y install httpd

#
# PHP
RUN yum -y install php

CMD ["/usr/sbin/apachectl", "-DFOREGROUND"]
