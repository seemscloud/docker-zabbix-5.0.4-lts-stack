FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /zabbix

RUN apt-get update &&\
    apt-get install -y postgresql wget

COPY ./archives/zabbix-5.0.4.tar.gz .
COPY ./files/pg_hba.conf /etc/postgresql/10/main/pg_hba.conf

RUN tar -xf zabbix-5.0.4.tar.gz && \
    chmod 640 /etc/postgresql/10/main/pg_hba.conf && \
    chown postgres:postgres /etc/postgresql/10/main/pg_hba.conf && \
    echo "listen_addresses = '*'" >> /etc/postgresql/10/main/postgresql.conf

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
     PGPASSWORD="zabbix-server" psql -h localhost -U "zabbix-server" "zabbix-server" < /zabbix/zabbix-5.0.4/database/postgresql/schema.sql && \
     PGPASSWORD="zabbix-server" psql -h localhost -U "zabbix-server" "zabbix-server" < /zabbix/zabbix-5.0.4/database/postgresql/images.sql && \
     PGPASSWORD="zabbix-server" psql -h localhost -U "zabbix-server" "zabbix-server" < /zabbix/zabbix-5.0.4/database/postgresql/data.sql

USER root

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

USER postgres

CMD ["/usr/lib/postgresql/10/bin/postgres", "-D", "/var/lib/postgresql/10/main", "-c", "config_file=/etc/postgresql/10/main/postgresql.conf"]
