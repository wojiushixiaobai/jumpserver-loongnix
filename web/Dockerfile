FROM cr.loongnix.cn/loongson/loongnix:20
WORKDIR /opt
ARG TARGETARCH \
    VERSION=v2.23.1
ENV VERSION=${VERSION} \
    LANG="en_US.UTF-8"

ARG DEPENDENCIES="                    \
        ca-certificates               \
        curl                          \
        nginx                         \
        wget"

RUN set -ex \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && apt-get update \
    && apt-get install -y --no-install-recommends $DEPENDENCIES \
    && wget https://github.com/jumpserver/luna/releases/download/${VERSION}/luna-${VERSION}.tar.gz \
    && tar -xf luna-${VERSION}.tar.gz \
    && mv luna-${VERSION} luna \
    && wget https://github.com/jumpserver/lina/releases/download/${VERSION}/lina-${VERSION}.tar.gz \
    && tar -xf lina-${VERSION}.tar.gz \
    && mv lina-${VERSION} lina \
    && echo "no" | dpkg-reconfigure dash \
    && rm -f /etc/nginx/sites-enabled/default \
    && rm -f /opt/*.tar.gz \
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*

ARG Jmservisor_VERSION=v1.2.3 \
    Client_VERSION=v1.1.6 \
    MRD_VERSION=10.6.7

RUN set -ex \
    && mkdir -p /opt/download \
    && cd /opt/download \
    && wget -q https://download.jumpserver.org/public/Microsoft_Remote_Desktop_${MRD_VERSION}_installer.pkg \
    && wget -qO /opt/download/Jmservisor.msi https://download.jumpserver.org/public/Jmservisor-${Jmservisor_VERSION}.msi \
    && wget -qO /opt/download/JumpServer-Client-Installer-x86_64.msi https://download.jumpserver.org/public/JumpServer-Client-Installer-${Client_VERSION}-x86_64.msi \
    && wget -qO /opt/download/JumpServer-Client-Installer.dmg https://download.jumpserver.org/public/JumpServer-Client-Installer-${Client_VERSION}.dmg \
    && wget -qO /opt/download/JumpServer-Client-Installer-amd64.run https://download.jumpserver.org/public/JumpServer-Client-Installer-${Client_VERSION}-amd64.run \
    && wget -qO /opt/download/JumpServer-Client-Installer-arm64.run https://download.jumpserver.org/public/JumpServer-Client-Installer-${Client_VERSION}-arm64.run

COPY web/jumpserver.conf /etc/nginx/conf.d/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
