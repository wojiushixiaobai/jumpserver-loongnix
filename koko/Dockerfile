FROM cr.loongnix.cn/loongson/loongnix:20
WORKDIR /opt/
ARG TARGETARCH \
    VERSION=v2.22.1
ENV VERSION=${VERSION} \
    LANG="en_US.UTF-8"

ARG DEPENDENCIES="                    \
        bash-completion               \
        ca-certificates               \
        curl                          \
        mariadb-client                \
        openssh-client                \
        wget"

RUN set -ex \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && apt-get update \
    && apt-get install -y --no-install-recommends $DEPENDENCIES \
    && echo "no" | dpkg-reconfigure dash \
    && rm -rf /var/lib/apt/lists/*

RUN set -ex \
    && wget https://github.com/jumpserver/koko/releases/download/${VERSION}/koko-${VERSION}-linux-${TARGETARCH}.tar.gz \
    && tar -xf koko-${VERSION}-linux-${TARGETARCH}.tar.gz \
    && mv koko-${VERSION}-linux-${TARGETARCH} koko \
    && chown -R root:root /opt/koko \
    && mv /opt/koko/kubectl /usr/local/bin/ \
    && chown root:root /usr/local/bin/kubectl \
    && wget https://download.jumpserver.org/public/kubectl-linux-${TARGETARCH}.tar.gz \
    && tar -xf kubectl-linux-${TARGETARCH}.tar.gz \
    && chmod 755 kubectl \
    && mv kubectl /usr/local/bin/rawkubectl \
    && wget http://download.jumpserver.org/public/kubectl_aliases.tar.gz \
    && mkdir /opt/kubectl-aliases/ \
    && tar -xf kubectl_aliases.tar.gz -C /opt/kubectl-aliases/ \
    && chown -R root:root /opt/kubectl-aliases/ \
    && chmod 755 /opt/koko/init-kubectl.sh \
    && wget https://download.jumpserver.org/redis/stable/redis-cli-linux-${TARGETARCH}.tar.gz \
    && tar -xf redis-cli-linux-${TARGETARCH}.tar.gz -C /usr/local/bin \
    && chmod 755 /usr/local/bin/redis-cli \
    && rm -rf /opt/*.tar.gz

COPY koko/entrypoint.sh .
RUN chmod 755 ./entrypoint.sh

CMD ["./entrypoint.sh"]
