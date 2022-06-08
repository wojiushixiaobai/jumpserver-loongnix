## Jumpserver Docker-Compose

环境要求
- MySQL Server >= 5.7
- Redis Server >= 5.0

快速部署
```sh
# 测试环境可以使用，生产环境推荐外置数据
cp config_example.conf .env
docker-compose -f docker-compose-network.yml -f docker-compose-redis.yml -f docker-compose-mysql.yml -f docker-compose-init-db.yml up -d
docker exec -i jms_core bash -c './jms upgrade_db'
docker-compose -f docker-compose-network.yml -f docker-compose-redis.yml -f docker-compose-mysql.yml -f docker-compose.yml up -d
```

标准部署

> 请先自行创建 数据库 和 Redis, 版本要求参考上面环境要求说明

```sh
cp config_example.conf .env
vi .env
```
```vim
# 版本号可以自己根据项目的版本修改
VERSION=v2.22.2

# Compose
COMPOSE_PROJECT_NAME=jms
COMPOSE_HTTP_TIMEOUT=3600
DOCKER_CLIENT_TIMEOUT=3600
DOCKER_SUBNET=172.16.238.0/24

# 持久化存储
VOLUME_DIR=/opt/jumpserver

# MySQL  # 填写你的 Mysql 服务器信息
DB_HOST=192.168.100.11
DB_PORT=3306
DB_USER=jumpserver
DB_PASSWORD=nu4x599Wq7u0Bn8EABh3J91G
DB_NAME=jumpserver

# Redis  # 填写你的 Redis 服务器信息
REDIS_HOST=192.168.100.12
REDIS_PORT=6379
REDIS_PASSWORD=8URXPL2x3HZMi7xoGTdk3Upj

# Core
SECRET_KEY=B3f2w8P2PfxIAS7s4URrD9YmSbtqX4vXdPUL217kL9XPUOWrmy
BOOTSTRAP_TOKEN=7Q11Vz6R2J6BLAdO
DEBUG=FALSE
LOG_LEVEL=ERROR

# Web  # 对外端口定义
HTTP_PORT=80
SSH_PORT=2222

##
# SECRET_KEY 保护签名数据的密匙, 首次安装请一定要修改并牢记, 后续升级和迁移不可更改, 否则将导致加密的数据不可解密。
# BOOTSTRAP_TOKEN 为组件认证使用的密钥, 仅组件注册时使用。组件指 koko、guacamole
```
```sh
docker-compose -f docker-compose-network.yml -f docker-compose-init-db.yml up -d
docker exec -i jms_core bash -c './jms upgrade_db'
docker-compose -f docker-compose-network.yml -f docker-compose.yml up -d
```
