# 第二课：Docker 与开发环境

## 一、Docker

**Docker = 把应用和运行环境一起打包，做到“一次构建，到处运行“的工具。**

### 1. 为什么需要 Docker

**痛点：**

- 本地 `Node 18` ，同事 `Node 20` ，服务器 `Node 16` 。
- 本地 `PostgreSQL 15` ，服务器 `PostgreSQL 13` 。
- 新人配环境要一天。
- 本地能跑，服务器跑不起来。

**Docker 解决：**

- 把环境变成代码。
- 环境可版本控制、可复现。
- 一条命令启动整套服务。

### 2. 核心概念

| 概念             | 说明                              | 类比       |
| :--------------- | :-------------------------------- | :--------- |
| 镜像 `Image`     | 只读模板，包含系统、依赖、代码    | 类         |
| 容器 `Container` | 镜像的运行实例                    | 对象       |
| 仓库 `Registry`  | 存放镜像的地方，默认 `Docker Hub` | `GitHub`   |
| `Dockerfile`     | 描述如何构建镜像                  | `Makefile` |
| `docker-compose` | 描述多个容器如何协作              | 编排脚本   |
| `Volume`         | 持久化数据，容器删了数据还在      | 外接硬盘   |
| `Network`        | 容器之间通信的网络                | 局域网     |

### 3. 容器 VS 虚拟机

|          | 容器           | 虚拟机             |
| :------- | :------------- | :----------------- |
| 隔离级别 | 进程级         | 硬件级             |
| 启动速度 | 秒级           | 分钟级             |
| 体积     | MB             | GB                 |
| 内核     | 共享宿主机内核 | 独立内核           |
| 性能     | 接近原生       | 有损耗             |
| 工具     | Docker         | VMware、VirtualBox |

一句话：

> 容器是轻量的进程隔离，虚拟机是完整的操作系统隔离。

## 二、Docker 命令

### 1. 镜像

```bash
docker pull postgres:15   # 拉取镜像
docker images             # 查看本地镜像
docker rmi <image_id>     # 删除镜像
```

### 2. 容器

```bash
docker run -d --name pg -p 5432:5432 -e POSTGRES_PASSWORD=secret postgres:15
docker ps                 # 查看运行中的容器
docker ps -a              # 查看所有容器
docker stop pg            # 停止
docker start pg           # 启动
docker rm pg              # 删除
docker logs pg            # 查看日志
docker exec -it pg bash   # 进入容器
```

`docker run` 参数解释：

- `-d`：后台运行
- `--name pg`：容器名叫 `pg`
- `-p 5432:5432`：宿主机端口:容器端口
- `-e POSTGRES_PASSWORD=secret`：环境变量
- `postgres:15`：镜像名:标签

### 3. 清理

```bash
docker system prune -a    # 清理未使用的镜像和容器
```

### 4. docker-compose

```bash
docker compose up -d              # 启动
docker compose down               # 停止
docker compose down -v            # 停止并删除数据
docker compose ps                 # 查看状态
docker compose logs -f postgres   # 查看日志
```

## 三、docker-compose 工作流

### 1. docker-compose 的作用

单个容器用 `docker-run` 还能忍受。

一旦项目需要多个服务，比如：

- `Web` 应用
- `PostgreSQL`
- `Redis`

用 `docker-run` 就要敲很多条命令，还要记住顺序、网络、卷、环境变量。

换一台机器又要重新再来一遍。

`docker-compose` 解决的问题：

> 用一个 `YAML` 文件，描述整个项目的服务、网络、卷、环境变量。一条命令就启动，一条命令就停止。

它是“开发环境即代码”的体现。

### 2. compose 文件

| 部分          | 作用                                         |
| ------------- | -------------------------------------------- |
| `services`    | 定义每个容器：用什么镜像、端口、环境变量、卷 |
| `volumes`     | 定义持久化数据卷                             |
| `networks`    | 定义容器之间的网络（默认会自动创建）         |
| `environment` | 环境变量，配置和代码分离                     |
| `ports`       | 宿主机端口和容器端口的映射                   |
| `depends_on`  | 启动顺序依赖                                 |

一个典型项目里：

- 数据库用官方镜像，不自己写 `Dockerfile` 。
- 应用自己写 `Dockerfile` ，用 `build` 而不是 `image`。
- 数据库数据挂 `volume` ，容器删了数据不丢。
- 应用通过服务名访问数据库，比如 `postgres:5432`，而不是 `localhost:5432`。

### 3. 服务名即主机名

在 `compose` 网络里：

- 容器之间用**服务名**互相访问。
- 不是 `localhost` ，不是 `127.0.0.1` 。
- 比如应用连数据库，`host` 写 `postgres` ，不是 `localhost` 。

原因：

- 每个容器有自己的网络命名空间。
- `localhost` 在容器里指容器自己。
- compose 会自动做 `DNS` ，把服务名解析到对应容器 `IP` 。

### 4. 端口映射的两个方向

```yaml
ports:
  - "5432:5432"
```

格式是 `宿主机端口 : 容器端口` 。

- 左边：你本机访问用的端口。
- 右边：容器内部服务监听的端口。

所以：

- 本机连数据库：`localhost:5432`
- 容器内应用连数据库：`postgres:5432`

不是一回事。

### 5. 数据持久化

容器默认是“无状态”的：

- 容器删了，里面的数据也没了。

所以数据库必须挂 volume：

```yaml
volumes:
  - pgdata:/var/lib/postgresql/data
```

区别：

- `docker compose down`：容器删了，`volume` 还在，数据还在。
- `docker compose down -v`：连 `volume` 一起删，数据没了。

### 6. 环境变量与配置分离

不要把用户名、密码、连接串写死在代码里。

正确做法：

- compose 里用 `environment` 或 `.env` 文件。
- 应用里用 `process.env.XXX` 读取。

好处：

- 本地、测试、生产可以用不同配置。
- 代码不用改，只改环境变量。
- 敏感信息不进 Git。

### 7. 常见坑

1. 用 `localhost` 连数据库 → 应该用服务名。
2. 忘了挂 `volume` → 重启数据丢。
3. 端口冲突 → 宿主机端口被占用。
4. `down -v` 误删数据。
5. 镜像版本用 `latest` → 不可复现，应该锁版本。
6. 启动顺序问题：应用比数据库先起，连不上。
   解决：应用里做重试，或用 `depends_on` + `healthcheck` 。

### 8. 工程视角

`docker-compose` 的意义不只是“启动方便”，而是：

- 开发环境可复现。
- 新人一条命令就能跑起来。
- 环境配置进版本控制。
- 本地和 `CI` 可以用同一套 `compose` 。
- 减少“在我机器上能跑”的问题。

## 四、真实场景：为什么本地能跑，服务器跑不起来

常见原因：

1. 依赖版本不一致：本地 Node 18，服务器 Node 16。
2. 环境变量没配：本地 `.env` 有，服务器没设。
3. 数据库没起：本地手动起了，服务器忘了。
4. 文件路径不对：Windows 路径 vs Linux 路径。
5. 端口冲突：服务器 5432 被占用。

Docker 的解法：

- 镜像里锁定版本。
- `docker-compose` 统一管理环境变量。
- `docker compose up` 一键起全套。
- 镜像内统一路径。
- 改映射端口。

一句话：

> Docker 把“环境”变成代码，环境问题就变成可版本控制、可复现的问题。

## 五、小练习

### 1. 用 Docker 跑起 PostgreSQL + Redis

**需求：**

- 在 `~/Engineering-Learning/practice/docker-lesson/` 下创建 `docker-compose.yml`
- 启动 `PostgreSQL 15` 和 `Redis 7`
- `PostgreSQL` 配置：
  - 用户名 `dev`
  - 密码 `dev`
  - 数据库 `devdb`
  - 端口映射 `5432:5432`
  - 数据持久化
- Redis 配置：
  - 端口映射 `6379:6379`
  - 数据持久化

**步骤：**

创建以下结构：

```text
~/Engineering-Learning/
	ports/
		第一课：工程思维与命令行.md
	practice/
		hello.sh
			docker-practice/
				docker-compose.yml
```

```bash
cd ~/Engineering-Learning/practice # 进入练习目录
mkdir -p docker-practice		   # 创建 docker 练习
cd docker-practice				   # 进入 docker 练习目录
nano docker-compose.yml			   # 创建并进入编辑 docker-compose.yml
```

在 `docker-compose.yml` 文件中写入以下内容

```yaml
# Docker Compose 配置文件的版本（现代 Compose 可省略此字段）
# 这里未显式声明 version，使用的是 Compose V2 的规范

services:
    # ===== PostgreSQL 数据库服务 =====
    postgres:
        # 使用的镜像：PostgreSQL 15 官方镜像
        image: postgres:15

        # 容器名称，方便用 docker ps / docker exec 时识别
        container_name: dev-postgres

        # 环境变量：PostgreSQL 镜像启动时会读取这些变量来初始化数据库
        environment:
            POSTGRES_USER: dev        # 创建的用户名
            POSTGRES_PASSWORD: dev    # 该用户的密码
            POSTGRES_DB: devdb        # 启动时自动创建的数据库名

        # 端口映射：宿主机端口:容器端口
        # 作用：让本机可以通过 localhost:5432 访问容器内的 Postgres
        ports:
            - "5432:5432"

        # 数据卷挂载：持久化数据，避免容器删除后数据丢失
        # pgdata 是下面定义的命名卷，挂载到容器内 Postgres 默认数据目录
        volumes:
            - pgdata:/var/lib/postgresql/data

    # ===== Redis 缓存服务 =====
    redis:
        # 使用的镜像：Redis 7 官方镜像
        image: redis:7

        # 容器名称
        container_name: dev-redis

        # 端口映射：宿主机 6379 → 容器 6379
        ports:
            - "6379:6379"

        # 数据卷挂载：持久化 Redis 数据（RDB/AOF 文件默认在 /data）
        volumes:
            - redisdata:/data

# ===== 命名卷声明 =====
# 这里只是声明卷名，Docker 会自动创建并管理它们
# 实际数据存储在宿主机的 Docker 数据目录中
volumes:
    pgdata:      # Postgres 数据卷
    redisdata:   # Redis 数据卷
```

保存并退出。

查看并进入 `PostgreSql` 和 `Redis` ：

```bash
docker compose up -d # 启动，可以看到两个容器启动读条，直到变成 Start
docker compose ps 	 # 查看状态，可以看到两个容器的各种状态

# Postgres
docker exec -it dev-postgres psql -U dev -d devdb
# 以交互模式进入名为 dev-postgres 的容器，并用 dev 用户连接到 devdb 数据库
SELECT version(); 	 # 查看 Posrgres 的版本
\q # 退出

# Redis
docker exec -it dev-redis redis-cli
# 以交互模式进入名为 dev-redis 的容器，并启动 redis-cli 客户端
SET hello world 	 # 把 key hello 的值设为字符串 world，设置成功会显示 OK
GET hello 			 # 读取 key hello 的值，返回 "world"（带引号是 redis-cli 的显示格式，表示这是字符串类型）
exit				 # 退出
```

### 2. 验证数据持久化

**需求：**

- 在 PostgreSQL 建表、插数据
- 在 Redis 设 key
- `docker compose down`
- `docker compose up -d`
- 重新检查数据
- `down` 再 `up` 后，数据还在
- 能解释 `down` 和 `down -v` 的区别

**步骤：**

```bash
docker exec -it dev-postgres psql -U dev -d devdb # 进入 Postgres
```

创建一张表，并插入一个值：

```sql
CREATE TABLE test_persist (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL
);
INSERT INTO test_persist (name) VALUES ('alice');
SELECT * FROM test_persist;
\q

/*
 id | name
----+-------
  1 | Alice
(1 row)
*/ 
```

```bash
docker exec -it dev-redis redis-cli # 进入 Redist
SET persist_key persist_value		# 设置键值对
GET persist_key						# 获取值
exit								# 退出
```

```bash
docker compose down  # 停止
docker compose up -d # 重启
```

再次验证：

```bash
docker exec -it dev-postgres psql -U dev -d devdb
SELECT * FROM test_persist;
\q

docker exec -it dev-redis redis-cli
GET persist_key
exit
```

重启后数据还在，持久化。

### 3. 测试环境不一致

**需求：**

- 把 `PostgreSQL 从` `15` 改成 `14`
- 重启，观察版本变化
- 改回 `15`
- `SELECT version();` 从 `15` 变成 `14`
- 能解释“换镜像版本等于换环境”

```bash
nano docker-compose.yml # 打开 compose 并修改 postgres:15 改成 postgres:14

docker compose down -v 							  # 停止，-v 表示删除持久化数据，否则切换 postgres 版本会重启失败
docker compose up -d							  # 重启

docker exec -it dev-postgres psql -U dev -d devdb # 进入 Postgres
SELECT version();								  # 查询版本
\q												  # 退出

# 改回 15
docker compose down
docker compose up -d
```
