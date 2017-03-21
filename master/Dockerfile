FROM ubuntu:16.04

# install postgres 9.4
RUN apt-get update && apt-get install --assume-yes gnupg-curl vim && \
    apt-key adv --fetch-keys https://www.postgresql.org/media/keys/ACCC4CF8.asc && \
    apt-key adv --fetch-keys http://packages.2ndquadrant.com/pglogical/apt/AA7A6805.asc && \
    echo 'deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main' > \
    	 /etc/apt/sources.list.d/pgdg.list && \
    echo 'deb http://packages.2ndquadrant.com/pglogical/apt/ xenial-2ndquadrant main' > \
    	 /etc/apt/sources.list.d/2ndquadrant.list && \
    apt-get update && apt-get install --assume-yes postgresql-9.4 postgresql-9.4-pglogical

# prepare config files
RUN ln -s /etc/postgresql/9.4/main/postgresql.conf /var/lib/postgresql/9.4/main/postgresql.conf && \
    ln -s /etc/postgresql/9.4/main/pg_hba.conf /var/lib/postgresql/9.4/main/pg_hba.conf

RUN echo 'wal_level = logical' >> /etc/postgresql/9.4/main/postgresql.conf && \
    echo 'shared_preload_libraries = pglogical' >> /etc/postgresql/9.4/main/postgresql.conf && \
    echo "listen_addresses = '*'" >> /etc/postgresql/9.4/main/postgresql.conf && \
    echo "max_replication_slots = 10" >> /etc/postgresql/9.4/main/postgresql.conf && \
    echo "max_wal_senders = 10" >> /etc/postgresql/9.4/main/postgresql.conf && \

    # FIXME: use hostnames
    echo "host test replication 172.19.0.3/16 md5" >> /var/lib/postgresql/9.4/main/pg_hba.conf && \
    echo "host replication replication 172.19.0.3/16 md5" >> /var/lib/postgresql/9.4/main/pg_hba.conf

# ensure temp dir exists
RUN mkdir -p /var/run/postgresql/9.4-main.pg_stat_tmp && \
    chown postgres:postgres /var/run/postgresql/9.4-main.pg_stat_tmp -R

# TODO: prepare DB
CMD su postgres -c "/usr/lib/postgresql/9.4/bin/postgres -D /var/lib/postgresql/9.4/main/"
