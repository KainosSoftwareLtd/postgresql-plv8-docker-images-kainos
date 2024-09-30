# ##############################################################################################
#
#    Based on https://github.com/plv8/plv8/blob/r3.1/platforms/Docker/bullseye/14.5/Dockerfile
#
#    Copyright (c) 2009-2022, the PLV8JS Development Group.
#
#    Permission to use, copy, modify, and distribute this software and its
#    documentation for any purpose, without fee, and without a written agreement
#    is hereby granted, provided that the above copyright notice and this
#    paragraph and the following two paragraphs appear in all copies.
#
#    IN NO EVENT SHALL the PLV8JS Development Group BE LIABLE TO ANY PARTY FOR
#    DIRECT, INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES, INCLUDING
#    LOST PROFITS, ARISING OUT OF THE USE OF THIS SOFTWARE AND ITS
#    DOCUMENTATION, EVEN IF the PLV8JS Development Group HAS BEEN ADVISED OF THE
#    POSSIBILITY OF SUCH DAMAGE.
#
#    The PLV8JS Development Group SPECIFICALLY DISCLAIMS ANY WARRANTIES,
#    INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY
#    AND FITNESS FOR A PARTICULAR PURPOSE. THE SOFTWARE PROVIDED HEREUNDER IS
#    ON AN "AS IS" BASIS, AND the PLV8JS Development Group HAS NO OBLIGATIONS TO
#    PROVIDE MAINTENANCE, SUPPORT, UPDATES, ENHANCEMENTS, OR MODIFICATIONS.
#
# ##############################################################################################

ARG PG_CONTAINER_VERSION=12.18
FROM docker.io/library/postgres:${PG_CONTAINER_VERSION}-bullseye AS builder

ARG DEBIAN_FRONTEND=noninteractive
RUN set -ex \
  && apt-get update \
  && apt-get install -y build-essential curl python3 ninja-build git wget postgresql-server-dev-${PG_MAJOR} libtinfo5 pkg-config clang binutils procps lsof man-db manpages-dev \
  && apt-get clean
ARG PLV8_BRANCH=r3.1
ENV PLV8_BRANCH=${PLV8_BRANCH}
ARG PLV8_VERSION=3.1.10
ENV PLV8_VERSION=${PLV8_VERSION}
RUN set -ex \
  && git clone --branch ${PLV8_BRANCH} --single-branch --depth 1 https://github.com/plv8/plv8 \
  && cd plv8 \
  && make DOCKER=1 generate_upgrades \
  && make DOCKER=1 install \
  && strip /usr/lib/postgresql/${PG_MAJOR}/lib/plv8-${PLV8_VERSION}.so


FROM docker.io/library/postgres:${PG_CONTAINER_VERSION}-bullseye

ARG PLV8_VERSION=3.1.10
ENV PLV8_VERSION=${PLV8_VERSION}
COPY --from=builder /usr/lib/postgresql/${PG_MAJOR}/lib/plv8* /usr/lib/postgresql/${PG_MAJOR}/lib/
COPY --from=builder /usr/lib/postgresql/${PG_MAJOR}/lib/bitcode/plv8-${PLV8_VERSION}.index.bc /usr/lib/postgresql/${PG_MAJOR}/lib/bitcode/
COPY --from=builder /usr/lib/postgresql/${PG_MAJOR}/lib/bitcode/plv8-${PLV8_VERSION}/* /usr/lib/postgresql/${PG_MAJOR}/lib/bitcode/plv8-${PLV8_VERSION}/
COPY --from=builder /usr/share/postgresql/${PG_MAJOR}/extension/plv8* /usr/share/postgresql/${PG_MAJOR}/extension/
COPY --from=builder /usr/share/postgresql/${PG_MAJOR}/extension/plls* /usr/share/postgresql/${PG_MAJOR}/extension/
COPY --from=builder /usr/share/postgresql/${PG_MAJOR}/extension/plcoffee* /usr/share/postgresql/${PG_MAJOR}/extension/


RUN mkdir -p /var/log/postgres \
  && touch /var/log/postgres/log /var/log/postgres/log.csv \
  && chown -R postgres /var/log/postgres
