FROM jumpserver/python:3.8-slim
ARG TARGETARCH \
    VERSION=v2.26.1
ENV VERSION=${VERSION} \
    LANG="en_US.UTF-8"

WORKDIR /opt

ARG BUILD_DEPENDENCIES="              \
        g++                           \
        make                          \
        pkg-config"

ARG DEPENDENCIES="                    \
        default-libmysqlclient-dev    \
        freetds-dev                   \
        libpq-dev                     \
        libffi-dev                    \
        libjpeg62-turbo-dev           \
        libldap2-dev                  \
        libsasl2-dev                  \
        libxml2-dev                   \
        libxmlsec1-dev                \
        libxmlsec1-openssl            \
        libaio-dev                    \
        openssh-client                \
        sshpass"

ARG TOOLS="                           \
        ca-certificates               \
        curl                          \
        default-mysql-client          \
        gettext                       \
        iproute2                      \
        iputils-ping                  \
        locales                       \
        netcat                        \
        redis-server                  \
        procps                        \
        telnet                        \
        vim                           \
        wget"

RUN set -ex \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && apt-get update \
    && apt-get -y install --no-install-recommends ${BUILD_DEPENDENCIES} \
    && apt-get -y install --no-install-recommends ${DEPENDENCIES} \
    && apt-get -y install --no-install-recommends ${TOOLS} \
    && echo "no" | dpkg-reconfigure dash \
    && mkdir -p /root/.ssh/ \
    && echo "Host *\n\tStrictHostKeyChecking no\n\tUserKnownHostsFile /dev/null" > /root/.ssh/config \
    && sed -i "s@# alias l@alias l@g" ~/.bashrc \
    && echo "set mouse-=a" > ~/.vimrc \
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*

RUN set -ex \
    && wget https://github.com/jumpserver/jumpserver/releases/download/${VERSION}/jumpserver-${VERSION}.tar.gz \
    && tar -xf jumpserver-${VERSION}.tar.gz \
    && mv jumpserver-${VERSION} jumpserver \
    && rm -f /opt/jumpserver/apps/common/utils/ip/geoip/GeoLite2-City.mmdb /opt/jumpserver/apps/common/utils/ip/ipip/ipipfree.ipdb \
    && wget https://download.jumpserver.org/files/ip/GeoLite2-City.mmdb -O /opt/jumpserver/apps/common/utils/ip/geoip/GeoLite2-City.mmdb \
    && wget https://download.jumpserver.org/files/ip/ipipfree.ipdb -O /opt/jumpserver/apps/common/utils/ip/ipip/ipipfree.ipdb \
    && chown -R root:root jumpserver \
    && rm -f /opt/*.tar.gz

RUN set -ex \
    && pip install https://download.jumpserver.org/pypi/simple/cryptography/cryptography-36.0.1-cp38-cp38-linux_loongarch64.whl -i https://pypi.douban.com/simple \
    && pip install https://download.jumpserver.org/pypi/simple/greenlet/greenlet-1.1.2-cp38-cp38-linux_loongarch64.whl -i https://pypi.douban.com/simple \
    && pip install $(grep 'PyNaCl' /opt/jumpserver/requirements/requirements.txt) -i https://pypi.douban.com/simple \
    && GRPC_PYTHON_BUILD_SYSTEM_OPENSSL=true pip install grpcio -i https://pypi.douban.com/simple \
    && pip install -r /opt/jumpserver/requirements/requirements.txt -i https://pypi.douban.com/simple \
    && cd /opt/jumpserver/apps \
    && sed -i "s@daemonize no@daemonize yes@g" /etc/redis/redis.conf \
    && sed -i "598i maxmemory-policy allkeys-lru" /etc/redis/redis.conf \
    && redis-server /etc/redis/redis.conf \
    && echo "SECRET_KEY: $(cat /dev/urandom | tr -dc A-Za-z0-9 | head -c 49)" > /opt/jumpserver/config.yml \
    && rm -f /opt/jumpserver/apps/locale/zh/LC_MESSAGES/django.mo /opt/jumpserver/apps/locale/zh/LC_MESSAGES/djangojs.mo \
    && python manage.py compilemessages \
    && rm -f /opt/jumpserver/config.yml \
    && cd /opt \
    && rm -f /opt/*.tar.gz \
    && rm -rf ~/.cache/pip

WORKDIR /opt/jumpserver

COPY core/entrypoint.sh .
RUN chmod 755 ./entrypoint.sh

EXPOSE 8070 8080

ENTRYPOINT ["./entrypoint.sh"]
