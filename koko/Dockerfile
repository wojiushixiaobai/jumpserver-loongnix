FROM cr.loongnix.cn/loongson/loongnix-server:8.3 as build_koko
WORKDIR /opt
ARG VERSION=v2.22.1 \
    GOPROXY=http://goproxy.loongnix.cn:3000 \
    GOSUMDB=off
ENV VERSION=${VERSION} \
    GO111MODULE=on \
    GOOS=linux

RUN set -ex \
    && yum -y install loongnix-release-epel \
    && cp /etc/yum.repos.d/Loongnix-Plus.repo /etc/yum.repos.d/Loongnix-Cloud.repo \
    && sed -i 's@/loongnixplus/$basearch/release/@/cloud/$basearch/release/kubernetes/@g' /etc/yum.repos.d/Loongnix-Cloud.repo \
    && sed -i 's@Plus@Cloud@g' /etc/yum.repos.d/Loongnix-Cloud.repo \
    && sed -i 's@plus@cloud@g' /etc/yum.repos.d/Loongnix-Cloud.repo \
    && sed -i 's@enabled=0@enabled=1@g' /etc/yum.repos.d/Loongnix-Cloud.repo \
    && yum -y install nodejs golang-1.18 git make kubectl wget \
    && mkdir /opt/koko-${VERSION} \
    && wget -O /opt/koko-${VERSION}.tar.gz https://github.com/jumpserver/koko/archive/refs/tags/${VERSION}.tar.gz \
    && tar -xf koko-${VERSION}.tar.gz -C /opt/koko-${VERSION} --strip-components 1 \
    && rm -f koko-${VERSION}.tar.gz \
    && cd /opt/koko-${VERSION} \
    && chmod 755 entrypoint.sh \
    && wget http://download.jumpserver.org/public/kubectl_aliases.tar.gz -O kubectl_aliases.tar.gz \
    && tar -xf kubectl_aliases.tar.gz \
    && rm -f kubectl_aliases.tar.gz \
    && chown root:root .kubectl_aliases \
    && cp /usr/bin/kubectl /opt/koko-${VERSION}/rawkubectl \
    && sed -i 's@h1:XDXtA5hveEEV8JB2l7nhMTp3t3cHp9ZpwcdjqyEWLlo=@h1:1E5jSco0WzcctqpsR+4GWHTfNwvnYvrWS5pidL5xRm4=@g' go.sum \
    && sed -i 's@h1:iocB37TsdFuN6IBRZ+ry36wrkoV51/tl5vOWqkcPGvY=@h1:d6kMnkQ1iaCmwi3Wrixj9D8tZEENM3b2Ct+IAHQZbpk=@g' go.sum \
    && sed -i 's@gitHash=.*@gitHash="f9fa7bf5fb37656e4f10e6cab183ed9da3622bb9"@g' utils/build.sh \
    && yum clean all \
    && rm -rf /var/cache/yum/*

RUN set -ex \
    && cd /opt/koko-${VERSION} \
    && go mod download -x \
    && go get -u github.com/creack/pty \
    && cd utils \
    && chmod 755 init-kubectl.sh \
    && sh build.sh \
    && cd /opt/koko-${VERSION}/ui \
    && npm install -g yarn \
    && npm install -i \
    && yarn build \
    && cd /opt/koko-${VERSION} \
    && mkdir -p release/koko/ui \
    && cp -rf ui/dist release/koko/ui \
    && cp utils/coredump.sh release/koko \
    && cp utils/init-kubectl.sh release/koko \
    && ls -al /opt/koko-${VERSION}/release/koko

FROM cr.loongnix.cn/loongson/loongnix:20 as build_redis
WORKDIR /opt

RUN set -ex \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates wget make gcc g++ pkg-config \
    && wget https://download.redis.io/redis-stable.tar.gz \
    && tar xf redis-stable.tar.gz \
    && rm -f redis-stable.tar.gz \
    && cd /opt/redis-stable \
    && rm -f deps/jemalloc/build-aux/config.guess deps/jemalloc/build-aux/config.sub \
    && wget -O deps/jemalloc/build-aux/config.sub "git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.sub;hb=HEAD" \
    && wget -O deps/jemalloc/build-aux/config.guess "git.savannah.gnu.org/gitweb/?p=config.git;a=blob_plain;f=config.guess;hb=HEAD" \
    && nproc="$(nproc)" \
    && make -j "$nproc" \
    && rm -rf /var/lib/apt/lists/*

FROM cr.loongnix.cn/loongson/loongnix:20
WORKDIR /opt/
ARG VERSION=v2.22.1
ENV VERSION=${VERSION} \
    LANG="en_US.UTF-8"

ARG DEPENDENCIES="                    \
    bash-completion                   \
    ca-certificates                   \
    curl                              \
    mariadb-client                    \
    openssh-client                    \
    wget"

RUN set -ex \
    && apt-get update \
    && apt-get install -y --no-install-recommends $DEPENDENCIES \
    && echo "no" | dpkg-reconfigure dash \
    && rm -rf /var/lib/apt/lists/*

COPY --from=build_koko /opt/koko-${VERSION}/release/koko /opt/koko
COPY --from=build_koko /opt/koko-${VERSION}/release/koko/kubectl /usr/local/bin/kubectl
COPY --from=build_koko /opt/koko-${VERSION}/rawkubectl /usr/local/bin/rawkubectl
COPY --from=build_koko /opt/koko-${VERSION}/.kubectl_aliases /opt/kubectl-aliases/.kubectl_aliases
COPY --from=build_redis /opt/redis-stable/src/redis-cli /usr/local/bin/redis-cli

WORKDIR /opt/koko

COPY koko/entrypoint.sh .
RUN chmod 755 ./entrypoint.sh

EXPOSE 2222 5000

ENTRYPOINT ["./entrypoint.sh"]
