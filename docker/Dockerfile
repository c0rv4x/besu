FROM ubuntu:24.04
ARG VERSION="dev"
ENV NO_PROXY_CACHE="-o Acquire::BrokenProxy=true -o Acquire::http::No-Cache=true -o Acquire::http::Pipeline-Depth=0"

# Update and install dependencies without using any cache
RUN apt-get update $NO_PROXY_CACHE && \
    apt-get -o Acquire::BrokenProxy=true -o Acquire::http::No-Cache=true -o Acquire::http::Pipeline-Depth=0 \
    --no-install-recommends -q --assume-yes install openjdk-21-jre-headless=21* libjemalloc-dev=5.* adduser=3* && \
    apt-get clean && \
    rm -rf /var/cache/apt/archives/* /var/cache/apt/archives/partial/* /var/lib/apt/lists/* && \
    # Starting from version 23.10, Ubuntu comes with an "ubuntu" user with uid 1000. We need 1000 for besu.
    userdel ubuntu 2>/dev/null || true && rm -rf /home/ubuntu && \
    # Ensure we use a stable UID for besu, as file permissions are tied to UIDs.
    adduser --uid 1000 --disabled-password --gecos "" --home /opt/besu besu && \
    chown besu:besu /opt/besu && chmod 0755 /opt/besu

ARG BESU_USER=besu
USER ${BESU_USER}
WORKDIR /opt/besu

COPY --chown=besu:besu besu /opt/besu/

# Expose services ports
# 8545 HTTP JSON-RPC
# 8546 WS JSON-RPC
# 8547 HTTP GraphQL
# 8550 HTTP ENGINE JSON-RPC
# 8551 WS ENGINE JSON-RPC
# 30303 P2P
EXPOSE 8545 8546 8547 8550 8551 30303

# defaults for host interfaces
ENV BESU_RPC_HTTP_HOST 0.0.0.0
ENV BESU_RPC_WS_HOST 0.0.0.0
ENV BESU_GRAPHQL_HTTP_HOST 0.0.0.0
ENV BESU_PID_PATH "/tmp/pid"

ENV OTEL_RESOURCE_ATTRIBUTES="service.name=besu,service.version=$VERSION"

ENV BESU_USER_NAME=${BESU_USER}
