FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

ARG ZBX_MAJOR_VERSION
ARG ZBX_PATCH_VERSION

WORKDIR /zabbix

RUN apt-get update
RUN apt-get install -y postgresql wget

RUN wget https://cdn.zabbix.com/zabbix/sources/stable/$ZBX_MAJOR_VERSION/zabbix-$ZBX_PATCH_VERSION.tar.gz -O zabbix-$ZBX_PATCH_VERSION.tar.gz
RUN tar -xf zabbix-$ZBX_PATCH_VERSION.tar.gz

COPY ./files/pg_hba.conf /etc/postgresql/10/main/pg_hba.conf
RUN chmod 640 /etc/postgresql/10/main/pg_hba.conf
RUN chown postgres:postgres /etc/postgresql/10/main/pg_hba.conf
RUN echo "listen_addresses = '*'" >> /etc/postgresql/10/main/postgresql.conf

USER postgres

RUN /etc/init.d/postgresql start && \
    psql -c "CREATE ROLE \"zabbix-server\";" && \
    psql -c "CREATE DATABASE \"zabbix-server\";" && \
    psql -c "ALTER DATABASE \"zabbix-server\" OWNER TO \"zabbix-server\";" && \
    psql -c "GRANT ALL PRIVILEGES ON DATABASE \"zabbix-server\" TO \"zabbix-server\";" && \
    psql -c "ALTER ROLE \"zabbix-server\" LOGIN;" && \
    psql -c "ALTER ROLE \"zabbix-server\" PASSWORD 'zabbix-server';" && \
    psql -c "ALTER DATABASE \"zabbix-server\" SET search_path=\"zabbix-server\";" && \
    psql -d "zabbix-server" -c "DROP SCHEMA \"public\";" && \
    psql -d "zabbix-server" -c "CREATE SCHEMA \"zabbix-server\";" && \
    psql -d "zabbix-server" -c "ALTER SCHEMA \"zabbix-server\" OWNER TO \"zabbix-server\";" && \
    psql -d "zabbix-server" -c "GRANT ALL PRIVILEGES ON SCHEMA \"zabbix-server\" TO \"zabbix-server\";" && \
    PGPASSWORD="zabbix-server" psql -h localhost -U "zabbix-server" "zabbix-server" < /zabbix/zabbix-$ZBX_PATCH_VERSION/database/postgresql/schema.sql && \
    PGPASSWORD="zabbix-server" psql -h localhost -U "zabbix-server" "zabbix-server" < /zabbix/zabbix-$ZBX_PATCH_VERSION/database/postgresql/images.sql && \
    PGPASSWORD="zabbix-server" psql -h localhost -U "zabbix-server" "zabbix-server" < /zabbix/zabbix-$ZBX_PATCH_VERSION/database/postgresql/data.sql

USER root

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

USER postgres

CMD ["/usr/lib/postgresql/10/bin/postgres", "-D", "/var/lib/postgresql/10/main", "-c", "config_file=/etc/postgresql/10/main/postgresql.conf"]