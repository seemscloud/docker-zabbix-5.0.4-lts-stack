FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

ARG ZBX_DB_SERVER_ADDR
ARG ZBX_SERVER_ADDR
ARG ZBX_MAJOR_VERSION
ARG ZBX_PATCH_VERSION

RUN apt-get update
RUN apt-get install -y apache2 libapache2-mod-php php php-gd php-xml php-bcmath php-mbstring php-pgsql php-ldap wget

RUN wget https://cdn.zabbix.com/zabbix/sources/stable/$ZBX_MAJOR_VERSION/zabbix-$ZBX_PATCH_VERSION.tar.gz -O zabbix-$ZBX_PATCH_VERSION.tar.gz
RUN tar -xf zabbix-$ZBX_PATCH_VERSION.tar.gz

RUN cp -R ./zabbix-$ZBX_PATCH_VERSION/ui/* /var/www/html
RUN rm -f /var/www/html/index.html

COPY ./files/php.ini /etc/php/7.2/apache2/php.ini
RUN chmod 644 /etc/php/7.2/apache2/php.ini

COPY ./files/zabbix.conf.php /var/www/html/conf/zabbix.conf.php
RUN chmod 644 /var/www/html/conf/zabbix.conf.php
RUN sed -i "s/ZBX_DB_SERVER_ADDR/$ZBX_DB_SERVER_ADDR/g" /var/www/html/conf/zabbix.conf.php
RUN sed -i "s/ZBX_SERVER_ADDR/$ZBX_SERVER_ADDR/g" /var/www/html/conf/zabbix.conf.php

RUN rm -f zabbix-$ZBX_PATCH_VERSION.tar.gz && \
    rm -rf zabbix-$ZBX_PATCH_VERSION

RUN apt-get autoremove --purge -y && \
    apt-get autoclean && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /var/cache/apt/* && \
    rm -rf /tmp/* && \
    rm -rf /var/tmp/* && \
    rm -rf /etc/ssh/ssh_host_*

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]