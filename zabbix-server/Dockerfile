FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /zabbix

ARG ZBX_DB_SERVER

RUN apt-get update && \
    apt-get install -y gcc make autoconf libcurl4 libssl-dev libldap2-dev curl libcurl4-openssl-dev postgresql-server-dev-all libxml2-dev unixodbc-dev libsnmp-dev libssh2-1-dev libopenipmi-dev libevent-dev telnet postgresql-client-10 wget

RUN useradd -m -d /opt/zabbix-server -s /bin/bash zabbix-server

COPY ./archives/zabbix-5.0.4.tar.gz .

RUN tar -xf zabbix-5.0.4.tar.gz &&\
    cd zabbix-5.0.4 && ./configure --prefix=/opt/zabbix-server --enable-server \
    --with-postgresql \
    --with-libxml2 \
    --with-unixodbc \
    --with-net-snmp \
    --with-ssh2 \
    --with-openipmi \
    --with-zlib \
    --with-libpthread \
    --with-libevent \
    --with-openssl \
    --with-ldap \
    --with-libcurl \
    --with-libpcre \
    --with-iconv && \
    make && \
    make install

RUN mv /opt/zabbix-server/etc/zabbix_server.conf /opt/zabbix-server/etc/zabbix_server.conf_BACKUP

COPY ./files/zabbix_server.conf /opt/zabbix-server/etc/zabbix_server.conf

RUN sed -i "s/ZBX_DB_SERVER/$ZBX_DB_SERVER/g" /opt/zabbix-server/etc/zabbix_server.conf &&\
    mkdir -p /opt/zabbix-server/log && \
    mkdir -p /opt/zabbix-server/tmp && \
    mkdir -p /opt/zabbix-server/pid && \
    find /opt/zabbix-server -type f -exec chmod 0600 {} \; && \
    find /opt/zabbix-server -type d -exec chmod 0700 {} \; && \
    find /opt/zabbix-server -exec chown -R zabbix-server:zabbix-server {} \; &&\
    chmod 700 /opt/zabbix-server/sbin/zabbix_server

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

USER zabbix-server

CMD ["/opt/zabbix-server/sbin/zabbix_server", "-f", "-c", "/opt/zabbix-server/etc/zabbix_server.conf"]
