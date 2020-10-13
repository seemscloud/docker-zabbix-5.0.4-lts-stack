FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /zabbix

ARG ZBX_DB_SERVER_ADDR
ARG ZBX_SERVER_ADDR

RUN apt-get update
RUN apt-get install -y apache2 libapache2-mod-php php php-gd php-xml php-bcmath php-mbstring php-pgsql php-ldap wget

COPY ./files/php.ini /etc/php/7.2/apache2/php.ini
COPY ./files/zabbix.conf.php /var/www/html/conf/zabbix.conf.php
COPY ./archives/zabbix-5.0.4.tar.gz .

RUN chmod 644 /etc/php/7.2/apache2/php.ini && \
    tar -xf zabbix-5.0.4.tar.gz && \
    cp -R ./zabbix-5.0.4/ui/* /var/www/html && \
    rm -f /var/www/html/index.html && \
    chmod 644 /var/www/html/conf/zabbix.conf.php && \
    sed -i "s/ZBX_DB_SERVER_ADDR/$ZBX_DB_SERVER_ADDR/g" /var/www/html/conf/zabbix.conf.php && \
    sed -i "s/ZBX_SERVER_ADDR/$ZBX_SERVER_ADDR/g" /var/www/html/conf/zabbix.conf.php

RUN rm -f zabbix-5.0.4.tar.gz && \
    rm -rf zabbix-5.0.4

RUN apt-get autoremove --purge -y && \
    apt-get autoclean && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /var/cache/apt/* && \
    rm -rf /tmp/* && \
    rm -rf /var/tmp/* && \
    rm -rf /etc/ssh/ssh_host_*

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]