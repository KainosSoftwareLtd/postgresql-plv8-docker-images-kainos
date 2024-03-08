FROM postgres:15.6-bullseye

MAINTAINER Bartek "Draakhan" Szymański <postgres-plv8-docker-images@draakhan.info>

ENV PLV8_VERSION=3.1.10
ENV MAKE_DEPENDENCIES="procps wget build-essential libc++-dev libc++abi-dev pkg-config libtinfo5 ca-certificates git python3 python2.7-dev ninja-build postgresql-server-dev-$PG_MAJOR"
ENV RUNTIME_DEPENDENCIES="libc++1"

# Uncomment these lines to build image locally and avoid issues with Kainos root certificate and ZScaler.
#
# Copy the actual certificate file to the same directory as the Dockerfile before building the image
# Where to find certificate: https://kainossoftwareltd.sharepoint.com/systems/SitePages/JAVA-Certificate-Problems.aspx
#
# WARNING: 1) Never push the certificate file to Github.
#          2) Never push the Docker image with a certificate file to Docker Hub, AWS ECR, any other hosting.
#ENV KAINOS_CERTIFICATE_CHAIN=/usr/share/ca-certificates/kainos-chain.crt
#COPY kainos-chain.pem ${KAINOS_CERTIFICATE_CHAIN}

# Build and install plv8 extension from sources.
RUN apt-get update \
    && apt-get install -y --no-install-recommends ${MAKE_DEPENDENCIES} ${RUNTIME_DEPENDENCIES} \
    && ln -s /usr/bin/python2.7 /usr/bin/python \
    && git config --global user.email "postgres-plv8-docker-images@draakhan.info" \
    && git config --global user.name "Bartek \"Draakhan\" Szymański" \
    && git config --global http.sslverify false \
    && git config --global https.sslverify false \
    && wget https://github.com/plv8/plv8/archive/v${PLV8_VERSION}.tar.gz \
    && tar -xvzf v${PLV8_VERSION}.tar.gz \
    # Newer approach described here: https://github.com/plv8/plv8/blob/v3.1.10/docs/BUILDING.md
    && cd plv8-${PLV8_VERSION} || { echo "Missing 'plv8-${PLV8_VERSION}' directory"; exit 1; } \
    # Disabling direct conversion between v8 and JSONB objects to avoid issues with fhir-engine
    # Fhir-engine was originally implemented to v8 version older than 3.0 which had disabled direct conversion by default
    && sed -i 's/CCFLAGS += -DJSONB_DIRECT_CONVERSION/#CCFLAGS += -DJSONB_DIRECT_CONVERSION/' Makefile \
    # Disabling BigInt breaking change introduced by plv8 3.0
    # See: https://github.com/plv8/plv8/issues/529
    && make BIGINT_GRACEFUL=1 \
    && make install \
    && cd .. \
    && apt-get clean \
    && apt-get remove -y ${MAKE_DEPENDENCIES} \
    && apt-get autoremove -y \
    && rm v${PLV8_VERSION}.tar.gz \
    && rm -rf plv8-${PLV8_VERSION} \
    && rm -rf /root/.vpython-root \
    && rm -rf /root/.vpython_cipd_cache \
    && rm -rf /var/lib/apt/lists/ \
    && rm -rf ${KAINOS_CERTIFICATE_CHAIN}
