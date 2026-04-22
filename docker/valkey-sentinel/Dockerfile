ARG VALKEY_VERSION=9.0.3

FROM docker.io/valkey/valkey:${VALKEY_VERSION} AS valkey-src

FROM docker.io/bitnami/minideb:trixie AS base

ARG VALKEY_VERSION
ARG TARGETARCH=amd64

COPY scripts/prebuildfs /

SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]

RUN install_packages ca-certificates libssl3 procps

# valkey binaries — github.com/valkey-io/valkey — BSD-3-Clause
# valkey-sentinel is the same valkey-server binary invoked with --sentinel
RUN mkdir -p /opt/bitnami/valkey-sentinel/bin
COPY --from=valkey-src /usr/local/bin/valkey-server /opt/bitnami/valkey-sentinel/bin/valkey-server
COPY --from=valkey-src /usr/local/bin/valkey-cli    /opt/bitnami/valkey-sentinel/bin/valkey-cli

RUN ln -s valkey-server /opt/bitnami/valkey-sentinel/bin/valkey-sentinel && \
    ln -s valkey-server /opt/bitnami/valkey-sentinel/bin/redis-server    && \
    ln -s valkey-cli    /opt/bitnami/valkey-sentinel/bin/redis-cli       && \
    ln -s valkey-server /opt/bitnami/valkey-sentinel/bin/redis-sentinel

COPY scripts/rootfs /
RUN chmod +x /opt/bitnami/scripts/valkey-sentinel/*.sh

RUN apt-get update && apt-get upgrade -y && \
    apt-get clean && rm -rf /var/lib/apt/lists /var/cache/apt/archives && \
    chmod g+rwX /opt/bitnami && \
    find / -perm /6000 -type f -exec chmod a-s {} \; || true && \
    /opt/bitnami/scripts/valkey-sentinel/postunpack.sh && \
    ln -s /opt/bitnami/scripts/valkey-sentinel/entrypoint.sh /entrypoint.sh && \
    ln -s /opt/bitnami/scripts/valkey-sentinel/run.sh /run.sh

ENV APP_VERSION="${VALKEY_VERSION}" \
    BITNAMI_APP_NAME="valkey-sentinel" \
    IMAGE_REVISION="1" \
    PATH="/opt/bitnami/valkey-sentinel/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

EXPOSE 26379
USER 1001
ENTRYPOINT ["/opt/bitnami/scripts/valkey-sentinel/entrypoint.sh"]
CMD ["/opt/bitnami/scripts/valkey-sentinel/run.sh"]
