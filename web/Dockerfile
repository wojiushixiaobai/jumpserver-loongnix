FROM cr.loongnix.cn/loongson/loongnix:20
WORKDIR /opt
ARG VERSION=v2.22.1
ENV VERSION=${VERSION}

ARG DEPENDENCIES="                    \
    ca-certificates                   \
    curl                              \
    nginx                             \
    wget"

RUN set -ex \
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
    && rm -rf /var/lib/apt/lists/*

COPY web/jumpserver.conf /etc/nginx/conf.d/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
