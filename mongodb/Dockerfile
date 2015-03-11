FROM server1.cracker.org:5000/myrhel7.0
MAINTAINER Boonchu Ngampairoijpibul<bigchoo@gmail.com>

ADD ks.cracker.org_Kickstart_RHEL7_rhel7.1-beta-core.repo /etc/yum.repos.d/
ADD epel.repo /etc/yum.repos.d/

RUN yum repolist all
RUN yum update -y --nogpgcheck
RUN yum install atop -y --nogpgcheck
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
