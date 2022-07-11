FROM cr.loongnix.cn/loongson/loongnix:20
ARG TARGETARCH \
    VERSION=v2.23.3
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

ARG WISP_VERSION=v0.0.7

RUN set -ex \
    && wget https://github.com/jumpserver/wisp/releases/download/${WISP_VERSION}/wisp-${WISP_VERSION}-linux-${TARGETARCH}.tar.gz \
    && tar -xf wisp-${WISP_VERSION}-linux-${TARGETARCH}.tar.gz \
    && mv wisp-${WISP_VERSION}-linux-${TARGETARCH}/wisp /usr/local/bin/ \
    && chown root:root /usr/local/bin/wisp \
    && chmod 755 /usr/local/bin/wisp \
    && wget https://github.com/jumpserver/magnus-release/releases/download/${VERSION}/magnus-${VERSION}-linux-${TARGETARCH}.tar.gz \
    && tar -xf magnus-${VERSION}-linux-${TARGETARCH}.tar.gz \
    && mv magnus-${VERSION}-linux-${TARGETARCH} /opt/magnus \
    && chmod 755 /opt/magnus/magnus \
    && chown -R root:root /opt/magnus \
    && rm -rf /opt/*.tar.gz

COPY magnus/entrypoint.sh .
RUN chmod 755 ./entrypoint.sh

EXPOSE 33060 33061

CMD ["./entrypoint.sh"]
