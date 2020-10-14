FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

ARG ZBX_SERVER
ARG ZBX_AGENT_HOSTNAME
ARG ZBX_ACTIVE_SERVER
ARG ZBX_BUFFER_SEND
ARG ZBX_BUFFER_SIZE
ARG ZBX_REFRESH_ACTIVE_CHECKS
ARG ZBX_LISTEN_PORT
ARG ZBX_LOG_FILE_SIZE
ARG ZBX_START_AGENTS
ARG ZBX_LISTEN_IP
ARG ZBX_TIMEOUT
ARG ZBX_ALLOW_ROOT
ARG ZBX_UNSAFE_USER_PARAMETERS
ARG ZBX_LOG_REMOTE_COMMANDS
ARG ZBX_MAJOR_VERSION
ARG ZBX_PATCH_VERSION

RUN apt-get update
RUN apt-get install -y gcc make autoconf libcurl4 libssl-dev libldap2-dev libcurl4-openssl-dev libxml2-dev unixodbc-dev libsnmp-dev libssh2-1-dev libopenipmi-dev libevent-dev wget

RUN useradd -m -d /opt/zabbix-agent -s /bin/bash zabbix-agent

RUN wget https://cdn.zabbix.com/zabbix/sources/stable/$ZBX_MAJOR_VERSION/zabbix-$ZBX_PATCH_VERSION.tar.gz -O zabbix-$ZBX_PATCH_VERSION.tar.gz
RUN tar -xf zabbix-$ZBX_PATCH_VERSION.tar.gz

RUN cd zabbix-$ZBX_PATCH_VERSION && \
    ./configure --prefix=/opt/zabbix-agent --enable-agent \
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
    make && make install

RUN mv /opt/zabbix-agent/etc/zabbix_agentd.conf /opt/zabbix-agent/etc/zabbix_agentd.conf_BACKUP
COPY ./files/zabbix_agentd.conf /opt/zabbix-agent/etc/zabbix_agentd.conf

RUN sed -i "s/ZBX_SERVER/$ZBX_SERVER/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_AGENT_HOSTNAME/$ZBX_AGENT_HOSTNAME/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_ACTIVE_SERVER/$ZBX_ACTIVE_SERVER/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_BUFFER_SEND/$ZBX_BUFFER_SEND/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_BUFFER_SIZE/$ZBX_BUFFER_SIZE/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_REFRESH_ACTIVE_CHECKS/$ZBX_REFRESH_ACTIVE_CHECKS/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_LISTEN_PORT/$ZBX_LISTEN_PORT/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_LOG_FILE_SIZE/$ZBX_LOG_FILE_SIZE/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_START_AGENTS/$ZBX_START_AGENTS/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_LISTEN_IP/$ZBX_LISTEN_IP/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_TIMEOUT/$ZBX_TIMEOUT/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_ALLOW_ROOT/$ZBX_ALLOW_ROOT/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_UNSAFE_USER_PARAMETERS/$ZBX_UNSAFE_USER_PARAMETERS/g" /opt/zabbix-agent/etc/zabbix_agentd.conf
RUN sed -i "s/ZBX_LOG_REMOTE_COMMANDS/$ZBX_LOG_REMOTE_COMMANDS/g" /opt/zabbix-agent/etc/zabbix_agentd.conf

RUN mkdir -p /opt/zabbix-agent/log
RUN mkdir -p /opt/zabbix-agent/tmp
RUN mkdir -p /opt/zabbix-agent/pid
RUN find /opt/zabbix-agent -type f -exec chmod 0600 {} \;
RUN find /opt/zabbix-agent -type d -exec chmod 0700 {} \;
RUN find /opt/zabbix-agent -exec chown -R zabbix-agent:zabbix-agent {} \;
RUN chmod 700 /opt/zabbix-agent/sbin/zabbix_agentd

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

USER zabbix-agent

CMD ["/opt/zabbix-agent/sbin/zabbix_agentd", "-f", "-c", "/opt/zabbix-agent/etc/zabbix_agentd.conf"]