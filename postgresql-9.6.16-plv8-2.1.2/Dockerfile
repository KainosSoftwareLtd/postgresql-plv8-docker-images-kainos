FROM postgres:9.6.16

MAINTAINER Bartek "Draakhan" Szymański <postgres-plv8-docker-images@draakhan.info>

ENV PLV8_VERSION=2.1.2
ENV MAKE_DEPENDENCIES="wget build-essential ca-certificates git python postgresql-server-dev-$PG_MAJOR"

# Build and install plv8 extension from sources.
RUN apt-get update \
    && apt-get install -y --no-install-recommends ${MAKE_DEPENDENCIES} \
    && wget https://github.com/plv8/plv8/archive/v${PLV8_VERSION}.tar.gz \
    && tar -xvzf v${PLV8_VERSION}.tar.gz \
# The maintainers of the plv8 extension forgot to change the version number in the makefile:
# https://github.com/plv8/plv8/blob/v2.1.2/Makefile
# https://github.com/plv8/plv8/commit/b4a81cdd1939d24d537132bb4b2f8ea85b3c915c
    && sed -i 's/2.1.1/2.1.2/g' plv8-${PLV8_VERSION}/Makefile \
    && make --directory plv8-${PLV8_VERSION} static \
    && make --directory plv8-${PLV8_VERSION} install \
# Clean up
    && apt-get clean \
    && apt-get remove -y ${MAKE_DEPENDENCIES} \
    && apt-get autoremove -y \
    && rm v${PLV8_VERSION}.tar.gz \
    && rm -rf plv8-${PLV8_VERSION} \
    && rm -rf /root/.vpython-root \
    && rm -rf /root/.vpython_cipd_cache \
    && rm -rf /var/lib/apt/lists/*
