FROM --platform=$BUILDPLATFORM alpine:3.15.10 as migrations-download
ARG WEB3SIGNER_VERSION
WORKDIR /usr/src/app

# Copy files in branch ${WEB3SIGNER_VERSION} from web3signer repository
RUN apk --no-cache add git && \
    git clone --depth 1 --branch ${WEB3SIGNER_VERSION} https://github.com/ConsenSys/web3signer.git

FROM --platform=$TARGETPLATFORM alpine:3.15.10

ARG NETWORK
ARG PGPASSWORD
ARG POSTGRES_DB
ARG POSTGRES_USER

# Install Java and Flyway
RUN apk add --no-cache openjdk11-jre wget postgresql-client && \
    wget -qO- https://repo1.maven.org/maven2/org/flywaydb/flyway-commandline/9.16.1/flyway-commandline-9.16.1-linux-x64.tar.gz | tar xvz \
    && mv flyway-9.16.1 /flyway \
    && ln -s /flyway/flyway /usr/local/bin/flyway

ENV MIGRATIONS_DIR=/flyway/sql \
    NETWORK=${NETWORK} \
    PGPASSWORD=${PGPASSWORD} \
    POSTGRES_DB=${POSTGRES_DB} \
    POSTGRES_USER=${POSTGRES_USER}

COPY entrypoint.sh /usr/local/bin/entrypoint.sh

RUN chmod +x /usr/local/bin/entrypoint.sh && rm -rf /flyway/sql/*

COPY --from=migrations-download /usr/src/app/web3signer/slashing-protection/src/main/resources/migrations/postgresql/* ${MIGRATIONS_DIR}/

ENTRYPOINT ["entrypoint.sh"]
