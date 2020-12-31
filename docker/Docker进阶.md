### Docker进阶

[TOC]

#### Docker Compose

##### 三步骤

1. Dockerfile
	- Dockerfile保证我们的项目在任何地方可以运行。
2. docker-compose.yml
	- services 什么是服务
	- docker-compose.yml这个文件怎么写
3. docker-compose up
	- 启动项目

作用：批量容器编排。

> 我自己理解

Compose是Docker官方的开源项目。需要安装！

`Dockerfile`让程序在任何地方运行。web服务、redis、mysql、nginx...多个容器。

Compose

```yaml
version: "3.8"
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

Compose：重要的概念

- 服务services，容器、应用。
- 项目project，一组关联的容器。

##### 安装

---

1. 运行这个命令下载当前稳定版本的Docker Compose

~~~shell
sudo curl -L "https://studysssmd.oss-cn-chengdu.aliyuncs.com/docker-compose-Linux-x86_64" -o /usr/local/bin/docker-compose
~~~

2. 将可执行权限应用于二进制文件

~~~shell
sudo chmod +x /usr/local/bin/docker-compose
~~~

3. 测试是否安装成功

~~~ shell
docker-compose --version
~~~

##### 体验

第一步创建一个用于测试的目录：

```shell
$ mkdir composetest
$ cd composetest
```

第二步创建一个app.py文件并写入如下的内容：

```python
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```

在这个例子中`redis`是redis容器在这些应用所在的网络下的主机名。

第三步在同一个目录下创建一个`requirements.txt`文件并写入以下的内容：

```shell
flask
redis
```

第四步在同一个目录下创建`Dockerfile`并写入以下的内容：

```dockerfile
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```

第五步在同一个目录下创建`docker-compose.yml` 并写入以下的内容：

```yaml
version: "3.8"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

最后我们运行命令`docker-compose up`：

~~~shell
$ docker-compose up
Building web
Step 1/10 : FROM python:3.7-alpine
 ---> 295b051ee125
Step 2/10 : WORKDIR /code
 ---> Using cache
 ---> 0aafe2a88077
Step 3/10 : ENV FLASK_APP=app.py
 ---> Using cache
 ---> ebbf0d8e0bab
Step 4/10 : ENV FLASK_RUN_HOST=0.0.0.0
 ---> Using cache
 ---> 25cc22eb893b
Step 5/10 : RUN apk add --no-cache gcc musl-dev linux-headers
 ---> Running in eba1565f9e10
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
(1/13) Installing libgcc (9.3.0-r2)
(2/13) Installing libstdc++ (9.3.0-r2)
(3/13) Installing binutils (2.34-r1)
(4/13) Installing gmp (6.2.0-r0)
(5/13) Installing isl (0.18-r0)
(6/13) Installing libgomp (9.3.0-r2)
(7/13) Installing libatomic (9.3.0-r2)
(8/13) Installing libgphobos (9.3.0-r2)
(9/13) Installing mpfr4 (4.0.2-r4)
(10/13) Installing mpc1 (1.1.0-r1)
(11/13) Installing gcc (9.3.0-r2)
(12/13) Installing linux-headers (5.4.5-r1)
(13/13) Installing musl-dev (1.1.24-r9)
Executing busybox-1.31.1-r16.trigger
OK: 153 MiB in 48 packages
Removing intermediate container eba1565f9e10
 ---> 3ba7730a1c96
Step 6/10 : COPY requirements.txt requirements.txt
 ---> 3b9bde09ffe1
Step 7/10 : RUN pip install -r requirements.txt
 ---> Running in ed28c3e2ef31
Collecting flask
  Downloading Flask-1.1.2-py2.py3-none-any.whl (94 kB)
Collecting redis
  Downloading redis-3.5.3-py2.py3-none-any.whl (72 kB)
Collecting Jinja2>=2.10.1
  Downloading Jinja2-2.11.2-py2.py3-none-any.whl (125 kB)
Collecting Werkzeug>=0.15
  Downloading Werkzeug-1.0.1-py2.py3-none-any.whl (298 kB)
Collecting click>=5.1
  Downloading click-7.1.2-py2.py3-none-any.whl (82 kB)
Collecting itsdangerous>=0.24
  Downloading itsdangerous-1.1.0-py2.py3-none-any.whl (16 kB)
Collecting MarkupSafe>=0.23
  Downloading MarkupSafe-1.1.1.tar.gz (19 kB)
Building wheels for collected packages: MarkupSafe
  Building wheel for MarkupSafe (setup.py): started
  Building wheel for MarkupSafe (setup.py): finished with status 'done'
  Created wheel for MarkupSafe: filename=MarkupSafe-1.1.1-cp37-cp37m-linux_x86_64.whl size=16913 sha256=1437572f5ecd237de1ff5935f6b1cddd2a6511439568f53f6bb036e3d4845af8
  Stored in directory: /root/.cache/pip/wheels/b9/d9/ae/63bf9056b0a22b13ade9f6b9e08187c1bb71c47ef21a8c9924
Successfully built MarkupSafe
Installing collected packages: MarkupSafe, Jinja2, Werkzeug, click, itsdangerous, flask, redis
Successfully installed Jinja2-2.11.2 MarkupSafe-1.1.1 Werkzeug-1.0.1 click-7.1.2 flask-1.1.2 itsdangerous-1.1.0 redis-3.5.3
Removing intermediate container ed28c3e2ef31
 ---> bc8a6812c49b
Step 8/10 : EXPOSE 5000
 ---> Running in b35145a4382c
Removing intermediate container b35145a4382c
 ---> 0ba5bf043a1c
Step 9/10 : COPY . .
 ---> 447aa032e5cc
Step 10/10 : CMD ["flask", "run"]
 ---> Running in d39e29483ecd
Removing intermediate container d39e29483ecd
 ---> 3805805a71c4

Successfully built 3805805a71c4
Successfully tagged composetest_web:latest
WARNING: Image for service web was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Pulling redis (redis:alpine)...
alpine: Pulling from library/redis
df20fa9351a1: Already exists
9b8c029ceab5: Pull complete
e983a1eb737a: Pull complete
de9de2cb6149: Pull complete
6a04900c891e: Pull complete
be5e938aa8ff: Pull complete
Digest: sha256:4015d7a6a0901920a3adfae3a538bf8489325738153948f95ca2b637944bdfe5
Status: Downloaded newer image for redis:alpine
Creating composetest_web_1   ... done
Creating composetest_redis_1 ... done
Attaching to composetest_redis_1, composetest_web_1
redis_1  | 1:C 10 Oct 2020 02:52:05.167 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1  | 1:C 10 Oct 2020 02:52:05.167 # Redis version=6.0.8, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1  | 1:C 10 Oct 2020 02:52:05.167 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis_1  | 1:M 10 Oct 2020 02:52:05.169 * Running mode=standalone, port=6379.
redis_1  | 1:M 10 Oct 2020 02:52:05.169 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis_1  | 1:M 10 Oct 2020 02:52:05.169 # Server initialized
redis_1  | 1:M 10 Oct 2020 02:52:05.169 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_1  | 1:M 10 Oct 2020 02:52:05.169 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo madvise > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled (set to 'madvise' or 'never').
redis_1  | 1:M 10 Oct 2020 02:52:05.169 * Ready to accept connections
web_1    |  * Serving Flask app "app.py"
web_1    |  * Environment: production
web_1    |    WARNING: This is a development server. Do not use it in a production deployment.
web_1    |    Use a production WSGI server instead.
web_1    |  * Debug mode: off
web_1    |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
~~~

然后使用尝试访问localhost:5000：

```shell
[root@hp jdk1.8]# curl localhost:5000
Hello World! I have been seen 6 times.
[root@hp jdk1.8]# curl localhost:5000
Hello World! I have been seen 7 times.
[root@hp jdk1.8]# curl localhost:5000
Hello World! I have been seen 8 times.
[root@hp jdk1.8]# curl localhost:5000
Hello World! I have been seen 9 times.
```

体验完成！

##### 网络规则

项目中的内容都在同一个网络下 （可以通过`域名/容器名字`来访问了），可以通过以下命令来查看：

```shell
docker network ls
```

入下图所示：

![QQ20201111-194035@2x](https://studysssmd.oss-cn-chengdu.aliyuncs.com/typora/QQ20201111-194035@2x.png)

##### yaml规则

```shell
# 3层
version: '' #1版本
services:   #2服务
	service1: 
		# 服务配置
		image: xx
		build: xx
		network: xx
# 3其他配置 网络/卷、全局规则
volumes:
networks:
configs:
```

##### 使用compose一键部署WP博客

#### Docker Swarm

##### 工作模式

Docker Engine 1.12 introduces swarm mode that enables you to create a cluster of one or more Docker Engines called a swarm. A swarm consists of one or more nodes: physical or virtual machines running Docker Engine 1.12 or later in swarm mode.

There are two types of nodes: [**managers**](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/#manager-nodes) and [**workers**](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/#worker-nodes).

![Swarm mode cluster](https://studysssmd.oss-cn-chengdu.aliyuncs.com/typora/swarm-diagram.png)

```shell
docker swarm init --advertise-addr 192.168.57.2
```

> Swarm initialized: current node (hxf4fysbmlwy37t7g13tqlcad) is now a manager.
>
> To add a worker to this swarm, run the following command:
>
>     docker swarm join --token SWMTKN-1-22nrxb33zz817smrkrgf31wgzrzabqnbi5w7s81fp2saulwc6p-8dk2km6apq6vgp50h8qvlklne 192.168.57.2:2377
>
> To add a manager to this swarm, run `docker swarm join-token manager` and follow the instructions.
>
> ```shell
> docker swarm join-token worker
> docker swarm join-token manager
> ```

```shell
docker swarm join --token SWMTKN-1-22nrxb33zz817smrkrgf31wgzrzabqnbi5w7s81fp2saulwc6p-8dk2km6apq6vgp50h8qvlklne 192.168.57.2:2377
This node joined a swarm as a worker.
```

```shell
docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
hxf4fysbmlwy37t7g13tqlcad *   server2             Ready               Active              Leader              19.03.13
e3wiohyu7fqkf7sdcvltzlosy     server3             Ready               Active                                  19.03.13
```

```shell
docker swarm join-token manager
To add a manager to this swarm, run the following command:
    docker swarm join --token SWMTKN-1-22nrxb33zz817smrkrgf31wgzrzabqnbi5w7s81fp2saulwc6p-2xuf0gl02av3zmh42spr7w3rf 192.168.57.2:2377
```

##### Raft协议

双主双从：假设一个节点挂了！其他节点是否可以用？

Raft协议：保证大多数节点存活才可以用，只要大于1台就可以用。

集群可用，至少3个主节点，并且存活大于1。

##### 体会

弹性、扩缩容！集群！

```shell
docker service --help
```

灰度发布：金丝雀发布！

```shell
docker service create -p 8888:80 --name my-nginx nginx
pxmygmk2ax7ey8wpr1eya4wcs
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged 
```

```shell
docker service ps my-nginx
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
yop4732tptez        my-nginx.1          nginx:latest        server5             Running             Running 18 seconds ago

docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
pxmygmk2ax7e        my-nginx            replicated          1/1                 nginx:latest        *:8888->80/tcp
```

```shell
docker service update --replicas 3 my-nginx
my-nginx
overall progress: 3 out of 3 tasks 
1/3: running   [==================================================>] 
2/3: running   [==================================================>] 
3/3: running   [==================================================>] 
```

```shell
docker service update --replicas 1 my-nginx
my-nginx
overall progress: 1 out of 1 tasks 
1/1: running   [==================================================>] 
verify: Service converged 

docker service ps my-nginx
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
yop4732tptez        my-nginx.1          nginx:latest        server5             Running             Running 10 minutes ago 
```

```shell
docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
pxmygmk2ax7e        my-nginx            replicated          1/1                 nginx:latest        *:8888->80/tcp
```

```shell
docker service scale my-nginx=5
my-nginx scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service converged 

docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
pxmygmk2ax7e        my-nginx            replicated          5/5                 nginx:latest        *:8888->80/tcp
```

```shell
docker service rm my-nginx
my-nginx

docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
```

> Docker swarm其实并不难
>
> 只要会搭建集群、会启动服务、动态管理容器就可以了！

##### 概念总结

swarm

集群的管理和编号。docker可以初始化一个swarm集群，其他节点可以加入。（manager、worker）

node

就是一个docker节点。多个节点就组成一个网络集群。（manager、worker）

service

任务，可以在管理节点或者工作节点来运行。

task

容器内的命令，细节任务！

ingress

#### Docker Stack

```shell
# 单机
docker-compose up -d wordpress.yaml
# 集群
docker stack deploy wordpress.yaml
```

#### Docker Secret

安全！配置密码，证书！

#### Docker Config

配置

容器单独是没有意义的，有意义的是容器编排

#### k8s

