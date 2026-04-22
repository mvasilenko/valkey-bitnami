# kubectl — github.com/kubernetes/kubernetes — Apache-2.0, Copyright The Kubernetes Authors
# alpine base — alpinelinux.org — MIT (build scripts); packages retain their own licenses

ARG KUBECTL_VERSION=1.34.0

FROM alpine:3

ARG KUBECTL_VERSION
ARG TARGETARCH=amd64

RUN apk add --no-cache bash ca-certificates && \
    wget -qO /usr/local/bin/kubectl \
      "https://dl.k8s.io/release/v${KUBECTL_VERSION}/bin/linux/${TARGETARCH}/kubectl" && \
    chmod +x /usr/local/bin/kubectl

USER 65534
