FROM jumpserver/guacd:1.4.0
ARG TARGETARCH \
    VERSION=v2.24.0
ENV VERSION=${VERSION} \
    LANG="en_US.UTF-8"

WORKDIR /opt/

ARG DEPENDENCIES="                    \
        ca-certificates               \
        curl                          \
        wget"

RUN set -ex \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && apt-get update \
    && apt-get install -y --no-install-recommends $DEPENDENCIES \
    && echo "no" | dpkg-reconfigure dash \
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*

RUN set -ex \
    && wget https://github.com/jumpserver/lion-release/releases/download/${VERSION}/lion-${VERSION}-linux-${TARGETARCH}.tar.gz \
    && tar -xf lion-${VERSION}-linux-${TARGETARCH}.tar.gz \
    && mv lion-${VERSION}-linux-${TARGETARCH} lion \
    && chown -R root:root /opt/lion \
    && rm -rf /opt/*.tar.gz

COPY lion/entrypoint.sh .
RUN chmod 755 ./entrypoint.sh

EXPOSE 8081

CMD ["./entrypoint.sh"]
