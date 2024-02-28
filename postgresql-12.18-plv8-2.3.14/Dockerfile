FROM postgres:12.18

MAINTAINER Bartek "Draakhan" Szymański <postgres-plv8-docker-images@draakhan.info>

ENV PLV8_VERSION=2.3.14
ENV MAKE_DEPENDENCIES="procps wget build-essential libc++-dev libc++abi-dev pkg-config libtinfo5 ca-certificates git python2.7-dev postgresql-server-dev-$PG_MAJOR"
ENV RUNTIME_DEPENDENCIES="libc++1"

# Build and install plv8 extension from sources.

RUN echo "deb http://deb.debian.org/debian bullseye main" > /etc/apt/sources.list.d/bullseye.list \
    && echo "deb http://deb.debian.org/debian bullseye-updates main" >> /etc/apt/sources.list.d/bullseye.list \
    && echo "deb http://security.debian.org bullseye-security main" >> /etc/apt/sources.list.d/bullseye.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends ${MAKE_DEPENDENCIES} ${RUNTIME_DEPENDENCIES} \
    && ln -s /usr/bin/python2.7 /usr/bin/python \
    && git config --global http.sslverify false \
    && git config --global user.email "postgres-plv8-docker-images@draakhan.info" \
    && git config --global user.name "Bartek \"Draakhan\" Szymański" \
    && wget https://github.com/plv8/plv8/archive/v${PLV8_VERSION}.tar.gz \
    && tar -xvzf v${PLV8_VERSION}.tar.gz \
    && make --directory plv8-${PLV8_VERSION} static \
    && make --directory plv8-${PLV8_VERSION} install \
    && apt-get clean \
    && apt-get remove -y ${MAKE_DEPENDENCIES} \
    && apt-get autoremove -y \
    && rm v${PLV8_VERSION}.tar.gz \
    && rm -rf plv8-${PLV8_VERSION} \
    && rm -rf /root/.vpython-root \
    && rm -rf /root/.vpython_cipd_cache \
    && rm -rf /var/lib/apt/lists/
