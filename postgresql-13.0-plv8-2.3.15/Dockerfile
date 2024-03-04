FROM postgres:13.0

MAINTAINER Bartek "Draakhan" Szymański <postgres-plv8-docker-images@draakhan.info>

ENV PLV8_VERSION=2.3.15
ENV MAKE_DEPENDENCIES="procps wget build-essential libc++-dev libc++abi-dev pkg-config libtinfo5 ca-certificates git python3 postgresql-server-dev-$PG_MAJOR"
ENV RUNTIME_DEPENDENCIES="libc++1"

# Uncomment this line to build image locally and avoid issues with Kainos root certificate and ZScaler.
#
# Copy the actual certificate file to the same directory as the Dockerfile before building the image
# Where to find certificate: https://kainossoftwareltd.sharepoint.com/systems/SitePages/JAVA-Certificate-Problems.aspx
#
# WARNING: 1) Never push the certificate file to Github.
#          2) Never push the Docker image with a certificate file to Docker Hub, AWS ECR, any other hosting.
COPY kainos-chain.pem /usr/share/ca-certificates/kainos-chain.crt

# Build and install plv8 extension from sources.
RUN apt-get update \
    && apt-get install -y --no-install-recommends ${MAKE_DEPENDENCIES} ${RUNTIME_DEPENDENCIES} \
    && git config --global user.email "postgres-plv8-docker-images@draakhan.info" \
    && git config --global user.name "Bartek \"Draakhan\" Szymański" \
    && git config --global http.sslverify false \
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
    && rm -rf /var/lib/apt/lists/ \
    && rm -rf /usr/share/ca-certificates/kainos-chain.crt
