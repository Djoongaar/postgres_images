FROM postgres:16.14-bookworm

ENV TZ=Europe/Moscow
ENV PGTZ=Europe/Moscow
ENV PGDATA=/var/lib/postgresql/data/16
ENV PGBACKREST_LOG_PATH=/var/log/pgbackrest
ENV LANG=ru_RU.UTF8
ENV LC_COLLATE=ru_RU.UTF8
ENV LC_CTYPE=ru_RU.UTF8
ENV LC_MONETARY=en_US.UTF8
ENV LC_NUMERIC=en_US.UTF8
ENV LC_TIME=en_US.UTF8
ENV LC_MESSAGES=en_US.UTF8
ENV POSTGRES_INITDB_ARGS="--locale-provider=icu --icu-locale=ru-RU --encoding='utf8'"

RUN apt-get update --fix-missing && \
    apt-get install -y \
        curl \
        wget \
        gpg \
        nano \
        libxml2 \
        libicu-dev \
        postgresql-server-dev-16 \
        postgresql-16-postgis-3 \
        postgresql-contrib-16 \
        postgresql-16-repack \
        lsb-release \
        apt-utils

# Install pgbackrest dependencies
RUN apt-get update --fix-missing && \
    apt-get install -y \
        python3-distutils \
        meson \
        gcc \
        git \
        libpq-dev \
        libssl-dev \
        libxml2-dev \
        pkg-config \
        liblz4-dev \
        libzstd-dev \
        libbz2-dev \
        libz-dev \
        libyaml-dev

# Build monitoring extensions
RUN mkdir -p /usr/share/postgresql/16/contrib; \
    cd /usr/share/postgresql/16/contrib; \
    git clone -b master --single-branch --depth 1 "https://github.com/postgrespro/pg_wait_sampling.git"; \
    git clone -b master --single-branch --depth 1 "https://github.com/powa-team/pg_stat_kcache.git"; \
    git clone -b master --single-branch --depth 1 "https://github.com/zubkov-andrei/pg_profile.git"; \
    make USE_PGXS=y -C pg_wait_sampling install; \
    make USE_PGXS=y -C pg_stat_kcache install; \
    make USE_PGXS=y -C pg_profile install;

# Build pgbackrest binaries
RUN mkdir -p /build; \
    wget -q -O - https://github.com/pgbackrest/pgbackrest/archive/release/2.56.0.tar.gz | tar zx -C /build && \
    meson setup /build/pgbackrest /build/pgbackrest-release-2.56.0 && \
    ninja -C /build/pgbackrest && \
    cp /build/pgbackrest/src/pgbackrest /usr/bin && \
    chmod 755 /usr/bin/pgbackrest && \
    pgbackrest --version

# Creating required catalogs for pgbackrest and PGDATA
RUN mkdir -p /var/lib/postgresql/data; \
    chmod g+s /var/lib/postgresql/data; \
    mkdir -p -m 700 $PGDATA; \
    chown -R postgres:postgres /var/lib/postgresql; \
    mkdir -p -m 770 $PGBACKREST_LOG_PATH; \
    chown postgres:postgres $PGBACKREST_LOG_PATH; \
    mkdir -p /var/spool/pgbackrest; \
    chown postgres:postgres /var/spool/pgbackrest; \
    chmod 755 /var/spool/pgbackrest; \
    mkdir -p /etc/pgbackrest; \
    mkdir -p /etc/pgbackrest/conf.d;

# Generate ru_RU locale
RUN sed -i '/en_US.UTF-8 UTF-8/s/^# //g' /etc/locale.gen && \
    sed -i '/ru_RU.UTF-8 UTF-8/s/^# //g' /etc/locale.gen && \
    locale-gen
