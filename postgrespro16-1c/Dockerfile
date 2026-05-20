FROM ubuntu:24.04

ENV DEBIAN_FRONTEND="noninteractive"
ENV TZ="Europe/Moscow"
ENV PGTZ="Europe/Moscow"
ENV PGBACKREST_LOG_PATH="/var/log/pgbackrest"
ENV LANG="en_US.UTF8"
ENV LC_COLLATE="ru_RU.UTF8"
ENV LC_CTYPE="ru_RU.UTF8"
ENV LC_MONETARY="en_US.UTF8"
ENV LC_NUMERIC="en_US.UTF8"
ENV LC_TIME="en_US.UTF8"
ENV LC_MESSAGES="en_US.UTF8"
ENV PATH="/opt/pgpro/1c-16/bin:${PATH}"

ENV GOSU_VERSION 1.17
RUN set -eux; \
	\
    apt update && \
	apt install -y \
		ca-certificates \
		dpkg \
		gnupg \
        wget \
        nano \
        make \
        curl \
        gpg \
        gcc \
        git \
	; \
	\
	dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')"; \
	wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch"; \
	wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc"; \
	\
# verify the signature
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4; \
	gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
	gpgconf --kill all; \
	rm -rf "$GNUPGHOME" /usr/local/bin/gosu.asc; \
	chmod +x /usr/local/bin/gosu; \
# verify that the binary works
	gosu --version; \
	gosu nobody true
RUN set -eux; ln -svf gosu /usr/local/bin/su-exec; su-exec nobody true # backwards compatibility (removed in PostgreSQL 17+)

RUN mkdir /docker-entrypoint-initdb.d

RUN wget https://repo.postgrespro.ru/1c/1c-16/keys/pgpro-repo-add.sh && \
    sh pgpro-repo-add.sh && \
    apt-get install postgrespro-1c-16 -y && \
    mkdir -p /var/lib/pgpro/1c-16/data && \
    chown -R postgres:postgres /var/lib/pgpro/

RUN sed -i '/en_US.UTF-8 UTF-8/s/^# //g' /etc/locale.gen && \
    sed -i '/ru_RU.UTF-8 UTF-8/s/^# //g' /etc/locale.gen && \
    locale-gen

ENV PGDATA /var/lib/pgpro/1c-16/data

RUN mkdir -p "$PGDATA" && chown -R postgres:postgres "$PGDATA" && chmod 1777 "$PGDATA"

VOLUME "$PGDATA"

COPY docker-entrypoint.sh docker-ensure-initdb.sh /usr/local/bin/
RUN ln -sT docker-ensure-initdb.sh /usr/local/bin/docker-enforce-initdb.sh
ENTRYPOINT ["docker-entrypoint.sh"]

STOPSIGNAL SIGINT

EXPOSE 5432
CMD ["postgres"]