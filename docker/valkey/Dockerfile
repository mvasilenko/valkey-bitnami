ARG VALKEY_VERSION=9.0.3

FROM docker.io/bitnami/minideb:bookworm AS base

ARG VALKEY_VERSION
ARG TARGETARCH=amd64

COPY scripts/prebuildfs /

SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]

RUN install_packages ca-certificates curl libgcc-s1 libgomp1 libssl3 libstdc++6 procps

# wait-for-port from github.com/bitnami/wait-for-port (Apache-2.0)
RUN mkdir -p /opt/bitnami/common/bin && \
    curl -fsSL "https://github.com/bitnami/wait-for-port/releases/download/v1.0.10/wait-for-port-linux-${TARGETARCH}.tar.gz" \
      -o /tmp/wfp.tar.gz && \
    tar -zxf /tmp/wfp.tar.gz -C /opt/bitnami/common/bin/ && \
    rm /tmp/wfp.tar.gz

# valkey binaries from official OSS image (Apache-2.0)
RUN mkdir -p /opt/bitnami/valkey/bin
COPY --from=docker.io/valkey/valkey:${VALKEY_VERSION} /usr/local/bin/valkey-server    /opt/bitnami/valkey/bin/valkey-server
COPY --from=docker.io/valkey/valkey:${VALKEY_VERSION} /usr/local/bin/valkey-cli       /opt/bitnami/valkey/bin/valkey-cli
COPY --from=docker.io/valkey/valkey:${VALKEY_VERSION} /usr/local/bin/valkey-benchmark /opt/bitnami/valkey/bin/valkey-benchmark

RUN ln -s valkey-server    /opt/bitnami/valkey/bin/valkey-sentinel  && \
    ln -s valkey-server    /opt/bitnami/valkey/bin/valkey-check-aof && \
    ln -s valkey-server    /opt/bitnami/valkey/bin/valkey-check-rdb && \
    ln -s valkey-server    /opt/bitnami/valkey/bin/redis-server      && \
    ln -s valkey-cli       /opt/bitnami/valkey/bin/redis-cli         && \
    ln -s valkey-server    /opt/bitnami/valkey/bin/redis-sentinel    && \
    ln -s valkey-benchmark /opt/bitnami/valkey/bin/redis-benchmark   && \
    ln -s valkey-server    /opt/bitnami/valkey/bin/redis-check-aof   && \
    ln -s valkey-server    /opt/bitnami/valkey/bin/redis-check-rdb

COPY scripts/rootfs /
RUN chmod +x /opt/bitnami/scripts/valkey/*.sh

RUN apt-get update && apt-get upgrade -y && \
    apt-get purge -y curl && apt-get autoremove -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives && \
    chmod g+rwX /opt/bitnami && \
    find / -perm /6000 -type f -exec chmod a-s {} \; || true && \
    /opt/bitnami/scripts/valkey/postunpack.sh && \
    ln -s /opt/bitnami/scripts/valkey/entrypoint.sh /entrypoint.sh && \
    ln -s /opt/bitnami/scripts/valkey/run.sh /run.sh

ENV APP_VERSION="${VALKEY_VERSION}" \
    BITNAMI_APP_NAME="valkey" \
    IMAGE_REVISION="1" \
    PATH="/opt/bitnami/common/bin:/opt/bitnami/valkey/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

EXPOSE 6379
USER 1001
ENTRYPOINT ["/opt/bitnami/scripts/valkey/entrypoint.sh"]
CMD ["/opt/bitnami/scripts/valkey/run.sh"]
