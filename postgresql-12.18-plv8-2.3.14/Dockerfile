FROM draakhan/postgresql-plv8:12.12-2.3.14

MAINTAINER Bartek "Draakhan" Szymański <postgres-plv8-docker-images@draakhan.info>

RUN apt-get update \
    && apt-get install -y --no-install-recommends postgresql-12=12.18-1.pgdg110+2 postgresql-client-12=12.18-1.pgdg110+2 postgresql-client-common=257.pgdg110+1 postgresql-common=257.pgdg110+1 \
# Clean up
    && apt-get autoremove -y
