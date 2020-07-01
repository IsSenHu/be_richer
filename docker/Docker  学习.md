> Docker  学习

- Docker概述
- Docker安装
- Docker命令
  - 镜像命令
  - 容器命令
  - 操作命令
  - ...
- Docker镜像
- 容器数据卷
- DockerFile
- Docker网络原理
- Idea整合Docker
- Docker Compose
- Docker Swarm
- CI\CD Jenkins

### Docker概述

#### Docker为什么会出现？

### Docker的历史



> DevOps（开发，运维）
>
> 更便捷的升级和扩缩容
>
> 更简单的系统运维
>
> 更高效的计算资源利用

`uname -r 环境查看`

> 阿里云镜像加速

### Docker的常用命令

```shell
docker version			# 显示docker的版本信息
docker info					# 显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help	 # 万能命令
```

可以去查看Docker的官方文档

#### 镜像命令

docker images 查看所有本地的主机上的镜像

```shell
[root@VM_0_10_centos ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat              latest              2eb5a120304e        2 weeks ago         647MB
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB
tomcat9             centos              088ef4b10585        17 months ago       730MB
sshd                centos              16500609cdeb        17 months ago       335MB
testimport/centos   v1.0                48f54e18b4e3        17 months ago       202MB
test                latest              d3a081564a1e        17 months ago       202MB
issenhuzyp/test     latest              d3a081564a1e        17 months ago       202MB
registry            latest              33fbbf4a24e5        18 months ago       24.2MB
busybox             latest              3a093384ac30        18 months ago       1.2MB
centos              latest              1e1148e4cc2c        19 months ago       202MB
centos              latest              1e1148e4cc2c        19 months ago       202MB
training/webapp     latest              6fae60ef3446        5 years ago         349MB
training/postgres   latest              6fa973bb3c26        6 years ago         365MB

# 解释
REPOSITORY	 镜像的仓库源
TAG					 镜像的标签
IMAGE ID		 镜像的id
CREATED			 镜像的创建时间
SIZE				 镜像的大小

# 可选项
-a, -all		# 列出所有镜像
-q, -quiet	# 只显示镜像的id
```

docker search 搜索镜像

```shell
[root@VM_0_10_centos ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9687                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3524                [OK]                
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   706                                     [OK]
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   77                                      

# 可选项，通过搜索来过滤
--filter=STARS=3000		# 搜索出来的镜像STARS就是大于3000的

[root@VM_0_10_centos ~]# docker search mysql --filter=STARS=3000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   9687                [OK]                
mariadb             MariaDB is a community-developed fork of MyS…   3524                [OK]                
```

docker pull 下载镜像

```shell
#  下载镜像 docker pull 镜像名 [:tag]
[root@VM_0_10_centos ~]# docker pull mysql 
Using default tag: latest # 如果不写tag，默认就是latest
latest: Pulling from library/mysql
8559a31e96f4: Pull complete # 分层下载，docker image的核心 联合文件系统
d51ce1c2e575: Pull complete 
c2344adc4858: Pull complete 
fcf3ceff18fc: Pull complete 
16da0c38dc5b: Pull complete 
b905d1797e97: Pull complete 
4b50d1c6b05c: Pull complete 
c75914a65ca2: Pull complete 
1ae8042bdd09: Pull complete 
453ac13c00a3: Pull complete 
9e680cd72f08: Pull complete 
a6b5dc864b6c: Pull complete 
Digest: sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6 # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # 真实地址

# 等价于它
docker pull mysql
docker pull docker.io/library/mysql:latest

# 指定版本下载
[root@VM_0_10_centos ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
8559a31e96f4: Already exists 
d51ce1c2e575: Already exists 
c2344adc4858: Already exists 
fcf3ceff18fc: Already exists 
16da0c38dc5b: Already exists 
b905d1797e97: Already exists 
4b50d1c6b05c: Already exists 
d85174a87144: Pull complete 
a4ad33703fa8: Pull complete 
f7a5433ce20d: Pull complete 
3dcd2a278b4a: Pull complete 
Digest: sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

[root@VM_0_10_centos ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat              latest              2eb5a120304e        2 weeks ago         647MB
mysql               5.7                 9cfcce23593a        2 weeks ago         448MB
mysql               latest              be0dbf01a0f3        2 weeks ago         541MB
```

docker rmi 删除镜像

```shell
[root@VM_0_10_centos ~]# docker rmi -f 9cfcce23593a # 删除指定的容器，多个id用空格隔开
Untagged: mysql:5.7
Untagged: mysql@sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
Deleted: sha256:9cfcce23593a93135ca6dbf3ed544d1db9324d4c40b5c0d56958165bfaa2d46a
Deleted: sha256:98de3e212919056def8c639045293658f6e6022794807d4b0126945ddc8324be
Deleted: sha256:17e8b88858e400f8c5e10e7cb3fbab9477f6d8aacba03b8167d34a91dbe4d8c1
Deleted: sha256:c04c087c2af9abd64ba32fe89d65e6d83da514758923de5da154541cc01a3a1e
Deleted: sha256:ab8bf065b402b99aec4f12c648535ef1b8dc954b4e1773bdffa10ae2027d3e00
[root@VM_0_10_centos ~]# docker rmi -f $(docker images -aq) # 删除全部的容器
Untagged: mysql:latest
Untagged: mysql@sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6
Deleted: sha256:be0dbf01a0f3f46fc8c88b67696e74e7005c3e16d9071032fa0cd89773771576
Deleted: sha256:086d66e8d1cb0d52e9337eabb11fb9b95960e2e1628d90100c62ea5e8bf72306
Deleted: sha256:f37c61ee1973b18c285d0d5fcf02da4bcdb1f3920981499d2a20b2858500a110
Deleted: sha256:e40b8bca7dc63fc8d188a412328e56caf179022f5e5d5b323aae57d233fb1069
Deleted: sha256:339f6b96b27eb035cbedc510adad2560132925a835f0afddbcc1d311c961c14b
Deleted: sha256:d38b06cdb26a5c98857ddbc6ef531d3f57b00e325c0c314600b712efc7ff6ab0
Deleted: sha256:09687cd9cdf4c704fde969fdba370c2d848bc614689712bef1a31d0d581f2007
Deleted: sha256:b704a4a65bf536f82e5d8b86e633d19185e26313de8380162e778feb2852011a
Deleted: sha256:c37206160543786228aa0cce738e85343173851faa44bb4dc07dc9b7dc4ff1c1
Deleted: sha256:12912c9ec523f648130e663d9d4f0a47c1841a0064d4152bcf7b2a97f96326eb
Deleted: sha256:57d29ad88aa49f0f439592755722e70710501b366e2be6125c95accc43464844
Deleted: sha256:b17c024283d0302615c6f0c825137da9db607d49a83d2215a79733afbbaeb7c3
Deleted: sha256:13cb14c2acd34e45446a50af25cb05095a17624678dbafbcc9e26086547c1d74
Untagged: hello-world:latest
Untagged: hello-world@sha256:d58e752213a51785838f9eed2b7a498ffa1cb3aa7f946dda11af39286c3db9a9
Deleted: sha256:bf756fb1ae65adf866bd8c456593cd24beb6a0a061dedf42b26a993176745f6b
```

#### 容器命令

说明：我们有了镜像才可以创建容器，下载一个centos镜像来测试学习

~~~shell
docker pull centos
~~~

新建容器并启动

 ~~~shell
docker run [可选参数] image

# 参数说明
--name="Name"		容器名字
-d 							后台方式运行，java nohup
-it							使用交互方式运行，进入容器查看内容
-p							指定容器的端口 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口（常用）
	-p 容器端口
	容器端口
-P							随机指定端口

# 测试，启动并进入容器
[root@VM_0_10_centos ~]# docker run -it centos /bin/bash
[root@9815bafe91d3 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# 退出并关闭容器
[root@9815bafe91d3 /]# exit
exit
l[root@VM_0_10_centos ~]# ls
3          coroutine_test.lua  max.lua        multiReturn.lua  nohup.out  store       test.lua         zookeeper.out
array.lua  logs                metatable.lua  myPrint.lua      out        string.lua  testModule2.lua
console    lua                 module.lua     nacos            rootcron   table.lua   testModule.lua
 ~~~

列出所有的运行中的容器

~~~shell
# docker ps 命令
			# 列出当前正在运行的容器
-a		# 列出当前正在运行的容器 + 历史运行过的容器
-n=?	# 显示最近创建的容器
-q		# 只显示容器的id

[root@VM_0_10_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

[root@VM_0_10_centos ~]# docker ps -a -n=2
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
9815bafe91d3        centos              "/bin/bash"         8 minutes ago       Exited (0) 6 minutes ago                       cool_blackwell
daa4365086a6        bf756fb1ae65        "/hello"            46 hours ago        Exited (0) 46 hours ago                        relaxed_brown

[root@VM_0_10_centos ~]# docker ps -aq
9815bafe91d3
daa4365086a6
fed0767e826e
2534a80efba6
651e4fc8a093
0729d581d73c
92cea7c428b2
ff586726cc61
46b7fa716078
c2d7fb8366cf
~~~

退出容器

 ~~~shell
exit					# 直接容器停止并退出
Ctrl + P + Q	# 容器不停止并退出

[root@VM_0_10_centos ~]# docker run -it centos /bin/bash
[root@486a66932f9d /]# [root@VM_0_10_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
486a66932f9d        centos              "/bin/bash"         41 seconds ago      Up 40 seconds                           boring_poincare
 ~~~

删除容器

~~~shell
docker rm 容器id										 # 删除指定容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq)	 			# 删除所有的容器
docker ps -a -q | xargs docker rm   # 删除所有的容器

[root@VM_0_10_centos ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                       PORTS               NAMES
486a66932f9d        centos              "/bin/bash"              3 minutes ago       Up 3 minutes                                     boring_poincare
9815bafe91d3        centos              "/bin/bash"              16 minutes ago      Exited (0) 14 minutes ago                        cool_blackwell
daa4365086a6        bf756fb1ae65        "/hello"                 46 hours ago        Exited (0) 46 hours ago                          relaxed_brown
fed0767e826e        2eb5a120304e        "catalina.sh run"        2 weeks ago         Exited (143) 46 hours ago                        amazing_bardeen
2534a80efba6        33fbbf4a24e5        "/entrypoint.sh /etc…"   17 months ago       Exited (2) 17 months ago                         vibrant_lalande
651e4fc8a093        33fbbf4a24e5        "/entrypoint.sh /etc…"   17 months ago       Exited (2) 17 months ago                         youthful_pare
0729d581d73c        16500609cdeb        "/run.sh"                17 months ago       Exited (137) 17 months ago                       quirky_dijkstra
92cea7c428b2        088ef4b10585        "/run.sh"                17 months ago       Exited (143) 17 months ago                       eager_newton
ff586726cc61        16500609cdeb        "/run.sh"                17 months ago       Exited (137) 17 months ago                       fervent_fermi
46b7fa716078        1e1148e4cc2c        "/bin/bash"              17 months ago       Exited (137) 17 months ago                       relaxed_boyd
c2d7fb8366cf        1e1148e4cc2c        "/bin/bash"              17 months ago       Exited (137) 17 months ago                       sad_clarke
[root@VM_0_10_centos ~]# docker rm 9815bafe91d3
9815bafe91d3
[root@VM_0_10_centos ~]# docker rm 486a66932f9d
Error response from daemon: You cannot remove a running container 486a66932f9d8f2ffaf24b826e69eda6f3c9004678f16088f82aa433455d5e87. Stop the container before attempting removal or force remove
[root@VM_0_10_centos ~]# docker ps -aq
486a66932f9d
daa4365086a6
fed0767e826e
2534a80efba6
651e4fc8a093
0729d581d73c
92cea7c428b2
ff586726cc61
46b7fa716078
c2d7fb8366cf
[root@VM_0_10_centos ~]# docker rm -f $(docker ps -aq)
486a66932f9d
daa4365086a6
fed0767e826e
2534a80efba6
651e4fc8a093
0729d581d73c
92cea7c428b2
ff586726cc61
46b7fa716078
c2d7fb8366cf
[root@VM_0_10_centos ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
~~~

启动和停止容器的操作

~~~shell
docker start 容器id			# 启动容器
docker restart 容器id		# 重启容器
docker stop 容器id			# 停止当前正在运行的容器
docker kill 容器id			# 强制停止当前容器
~~~

### 常用其他命令

后台启动容器

~~~shell
#  命令 docker run -d 镜像名
[root@VM_0_10_centos ~]# docker run -d centos
855c437bb6d9732026a0ae67de79bf7971f13b36cb5f51a99598d13c1af51c92
[root@VM_0_10_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@VM_0_10_centos ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
855c437bb6d9        centos              "/bin/bash"         8 seconds ago       Exited (0) 7 seconds ago                       inspiring_ardinghelli
9d30576a0fef        centos              "/bin/bash"         24 minutes ago      Exited (0) 3 minutes ago                       wonderful_feynman
# 问题 docker ps. 发现 centos停止了	
# 常见的坑，docker 容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
~~~

#### 查看日志

~~~shell
docker logs -f -t --tail number 容器id

[root@VM_0_10_centos ~]# docker run -d centos /bin/bash -c "while true;do echo husen;sleep 1;done"
ae29b8b349893683c4d94d84567a99dd275dd867218086bfa5120a30bc25cfd9
[root@VM_0_10_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
ae29b8b34989        centos              "/bin/bash -c 'while…"   4 seconds ago       Up 2 seconds                            pedantic_ellis
[root@VM_0_10_centos ~]# docker logs -f -t --tail 10 ae29b8b34989
2020-06-30T09:11:55.007108677Z husen
2020-06-30T09:11:56.008845656Z husen
2020-06-30T09:11:57.010871504Z husen
2020-06-30T09:11:58.013034999Z husen
2020-06-30T09:11:59.014470467Z husen
2020-06-30T09:12:00.015965379Z husen
2020-06-30T09:12:01.017646323Z husen
2020-06-30T09:12:02.019715176Z husen
2020-06-30T09:12:03.021464027Z husen
2020-06-30T09:12:04.023164192Z husen
2020-06-30T09:12:05.025369672Z husen
2020-06-30T09:12:06.027315316Z husen
2020-06-30T09:12:07.028780719Z husen
2020-06-30T09:12:08.030391238Z husen
2020-06-30T09:12:09.032408390Z husen
2020-06-30T09:12:10.033980824Z husen
2020-06-30T09:12:11.035549689Z husen
2020-06-30T09:12:12.037082376Z husen
2020-06-30T09:12:13.040774862Z husen
2020-06-30T09:12:14.041316961Z husen
2020-06-30T09:12:15.046554460Z husen
2020-06-30T09:12:16.048226065Z husen
2020-06-30T09:12:17.049942962Z husen

-tf						# 显示日志
-tail number	# 要显示的日志条数

~~~

#### 查看容器中的进程信息

 ~~~shell
[root@VM_0_10_centos ~]# docker top ae29b8b34989
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                23125               23109               0                   17:10               ?                   00:00:00            /bin/bash -c while true;do echo husen;sleep 1;done
root                23796               23125               0                   17:16               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
 ~~~

#### 查看镜像元数据

~~~shell
[root@VM_0_10_centos ~]# docker inspect ae29b8b34989
[
    {
        "Id": "ae29b8b349893683c4d94d84567a99dd275dd867218086bfa5120a30bc25cfd9",
        "Created": "2020-06-30T09:10:38.771229245Z",
        "Path": "/bin/bash",
        "Args": [
            "-c",
            "while true;do echo husen;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 23125,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-06-30T09:10:39.797656021Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:831691599b88ad6cc2a4abbd0e89661a121aff14cfa289ad840fd3946f274f1f",
        "ResolvConfPath": "/var/lib/docker/containers/ae29b8b349893683c4d94d84567a99dd275dd867218086bfa5120a30bc25cfd9/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/ae29b8b349893683c4d94d84567a99dd275dd867218086bfa5120a30bc25cfd9/hostname",
        "HostsPath": "/var/lib/docker/containers/ae29b8b349893683c4d94d84567a99dd275dd867218086bfa5120a30bc25cfd9/hosts",
        "LogPath": "/var/lib/docker/containers/ae29b8b349893683c4d94d84567a99dd275dd867218086bfa5120a30bc25cfd9/ae29b8b349893683c4d94d84567a99dd275dd867218086bfa5120a30bc25cfd9-json.log",
        "Name": "/pedantic_ellis",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/736a457de7e053f3baeee4515ae06611b9aaaabf805aac07d52daacbed05c243-init/diff:/var/lib/docker/overlay2/667fe461782eb909ab8b3ebf113834a1109ddcd7bc27d1bb8e29916106e45e7f/diff",
                "MergedDir": "/var/lib/docker/overlay2/736a457de7e053f3baeee4515ae06611b9aaaabf805aac07d52daacbed05c243/merged",
                "UpperDir": "/var/lib/docker/overlay2/736a457de7e053f3baeee4515ae06611b9aaaabf805aac07d52daacbed05c243/diff",
                "WorkDir": "/var/lib/docker/overlay2/736a457de7e053f3baeee4515ae06611b9aaaabf805aac07d52daacbed05c243/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "ae29b8b34989",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash",
                "-c",
                "while true;do echo husen;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200611",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "1a9bb7879afd37972e2b69e4edd58dba708d1d4d63838a33054fecd00f950d33",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/1a9bb7879afd",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "d4980ac8d21d802d155f16a2de1e10d8d9b9821c6f3e13870c840014d3d2aa05",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "1cc457aee8bbdfff9c86b0fcec6fb2cf7ce0e88bd850e1b7ae736e1480c5f7bf",
                    "EndpointID": "d4980ac8d21d802d155f16a2de1e10d8d9b9821c6f3e13870c840014d3d2aa05",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
~~~

#### 进入当前正在运行的容器

~~~shell
# 我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置
# 命令
docker exec -it 容器id bashShell

# 测试
[root@VM_0_10_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
7709c1bddc90        centos              "/bin/bash -c 'while…"   9 seconds ago       Up 7 seconds                            cool_lovelace
[root@VM_0_10_centos ~]# docker exec -it 7709c1bddc90 /bin/bash
[root@7709c1bddc90 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 方式二
docker attach 容器id
 
[root@VM_0_10_centos ~]# docker attach 7709c1bddc90
 正在执行当前的代码......
 
# docker exec		进入容器后开启一个新的终端，可以在里面操作
# docker attach 进入容器正在执行的终端，不会开启一个新的终端
~~~

#### 从容器中拷贝文件到主机上

~~~shell
docker cp 容器id:容器内路径 目的的主机路径

# 测试
[root@VM_0_10_centos home]# docker exec -it 242737cb3409 /bin/bash	# 进入容器
[root@242737cb3409 /]# cd /home/	# 进入home目录
[root@242737cb3409 home]# ls
[root@242737cb3409 home]# touch test.java	# 创建一个文件
[root@242737cb3409 home]# ls
test.java
[root@242737cb3409 home]# exit	# 退回到主机
exit
[root@VM_0_10_centos home]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
242737cb3409        centos              "/bin/bash"         2 minutes ago       Up 2 minutes                            mystifying_brattain
[root@VM_0_10_centos home]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
242737cb3409        centos              "/bin/bash"         2 minutes ago       Up 2 minutes                            mystifying_brattain
[root@VM_0_10_centos home]# docker cp 242737cb3409:/home/test.java /home	# 从容器拷贝文件到指定目录
[root@VM_0_10_centos home]# ls
dev  es  husen.java  mysftp  test.java  user	# 这里已经有了test.java
~~~

### 一堆的练习

#### Docker 安装 Nginx

~~~shell
# 1、搜索镜像 search 建议去docker hub上搜索，可以看到帮助文档
[root@VM_0_10_centos ~]# docker search nginx
NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
nginx                              Official build of Nginx.                        13403               [OK]                
jwilder/nginx-proxy                Automated Nginx reverse proxy for docker con…   1829                                    [OK]
richarvey/nginx-php-fpm            Container running Nginx + PHP-FPM capable of…   779                                     [OK]
linuxserver/nginx                  An Nginx container, brought to you by LinuxS…   118                                     
bitnami/nginx                      Bitnami nginx Docker Image                      86                                      [OK]
tiangolo/nginx-rtmp                Docker image with Nginx using the nginx-rtmp…   81                                      [OK]
alfg/nginx-rtmp                    NGINX, nginx-rtmp-module and FFmpeg from sou…   70                                      [OK]
jc21/nginx-proxy-manager           Docker container for managing Nginx proxy ho…   67                                      
nginxdemos/hello                   NGINX webserver that serves a simple page co…   55                                      [OK]
jlesage/nginx-proxy-manager        Docker container for Nginx Proxy Manager        47                                      [OK]
nginx/nginx-ingress                NGINX Ingress Controller for Kubernetes         36                                      
privatebin/nginx-fpm-alpine        PrivateBin running on an Nginx, php-fpm & Al…   28                                      [OK]
schmunk42/nginx-redirect           A very simple container to redirect HTTP tra…   18                                      [OK]
nginxinc/nginx-unprivileged        Unprivileged NGINX Dockerfiles                  16                                      
nginx/nginx-prometheus-exporter    NGINX Prometheus Exporter                       13                                      
centos/nginx-112-centos7           Platform for running nginx 1.12 or building …   13                                      
raulr/nginx-wordpress              Nginx front-end for the official wordpress:f…   13                                      [OK]
centos/nginx-18-centos7            Platform for running nginx 1.8 or building n…   13                                      
blacklabelops/nginx                Dockerized Nginx Reverse Proxy Server.          13                                      [OK]
mailu/nginx                        Mailu nginx frontend                            7                                       [OK]
sophos/nginx-vts-exporter          Simple server that scrapes Nginx vts stats a…   7                                       [OK]
bitnami/nginx-ingress-controller   Bitnami Docker Image for NGINX Ingress Contr…   6                                       [OK]
bitwarden/nginx                    The Bitwarden nginx web server acting as a r…   6                                       
wodby/nginx                        Generic nginx                                   1                                       [OK]
ansibleplaybookbundle/nginx-apb    An APB to deploy NGINX                          1                                       [OK]

# 2、下载镜像 pull
[root@VM_0_10_centos ~]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
8559a31e96f4: Pull complete 
8d69e59170f7: Pull complete 
3f9f1ec1d262: Pull complete 
d1f5ff4f210d: Pull complete 
1e22bfa8652e: Pull complete 
Digest: sha256:21f32f6c08406306d822a0e6e8b7dc81f53f336570e852e25fbe1e3e3d0d0133
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
[root@VM_0_10_centos ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              831691599b88        2 weeks ago         215MB
tomcat              latest              2eb5a120304e        2 weeks ago         647MB
nginx               latest              2622e6cca7eb        3 weeks ago         132MB
registry            latest              33fbbf4a24e5        18 months ago       24.2MB
busybox             latest              3a093384ac30        18 months ago       1.2MB
centos              latest              1e1148e4cc2c        19 months ago       202MB
training/webapp     latest              6fae60ef3446        5 years ago         349MB
training/postgres   latest              6fa973bb3c26        6 years ago         365MB
# 3、运行测试 -d后台运行 --name起名字 -p 容器端口映射到主机端口
[root@VM_0_10_centos ~]# docker run -d --name nginx01 -p 3344:80 nginx
ebee4311f5a0c1e455902a25423142b7c145fefc6e1f2d5d4c971ef98e013658
[root@VM_0_10_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
ebee4311f5a0        nginx               "/docker-entrypoint.…"   6 seconds ago       Up 4 seconds        0.0.0.0:3344->80/tcp   nginx01
[root@VM_0_10_centos ~]# curl 'http://localhost:3344'
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
~~~

#### Docker  安装 Tomcat

~~~shell
# 官方的使用
docker run -it --rm tomcat:9.0
# 我们之前的启动都是后台，停止了容器之后，容器还是可以看到	docker run -it --rm tomcat:9.0，一般用来测试，用完就删除

# 下载
[root@VM_0_10_centos ~]# docker pull tomcat
Using default tag: latest
latest: Pulling from library/tomcat
Digest: sha256:81c2a95e5b1b5867229d75255abe54928d505deb81c8ff8949b61fde1a5d30a1
Status: Downloaded newer image for tomcat:latest
docker.io/library/tomcat:latest
# 启动运行
[root@VM_0_10_centos ~]# docker run -d -p 3355:8080 --name tomcat01 tomcat
81580192a2db9cf4a3b0628cb90369a3acb6e70b777e8a8da8144f1916e0d80d
# 测试没问题
~~~

#### Docker 部署 es + kibana

~~~shell
# es 暴露的端口很多！
# es 十分的耗内存
# es 的数据一把需要放置到安全目录！挂载
# --net somenetwork ？网络配置
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag

# 启动
[root@VM_0_10_centos ~]# docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
Unable to find image 'elasticsearch:7.6.2' locally
7.6.2: Pulling from library/elasticsearch
ab5ef0e58194: Pull complete 
c4d1ca5c8a25: Pull complete 
941a3cc8e7b8: Pull complete 
43ec483d9618: Pull complete 
c486fd200684: Pull complete 
1b960df074b2: Pull complete 
1719d48d6823: Pull complete 
Digest: sha256:1b09dbd93085a1e7bca34830e77d2981521a7210e11f11eda997add1c12711fa
Status: Downloaded newer image for elasticsearch:7.6.2
1e487d7ae5e8149f39d277e232d04265216e1c26be661a78c60a70df4cccb8bd

# 启动了如果linux卡死住了 docker stats 查看cpu的状态
# 修改配置文件 -e 环境配置修改
[root@VM_0_10_centos bin]# docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
192b61492fb79b2d751bafcd7d348a59382d36af6037ee7d3222c3801312919a
[root@VM_0_10_centos bin]# docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                            NAMES
192b61492fb7        elasticsearch:7.6.2   "/usr/local/bin/dock…"   3 seconds ago       Up 2 seconds        0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp   elasticsearch
[root@VM_0_10_centos bin]# docker stats

CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
192b61492fb7        elasticsearch       0.00%               170.5MiB / 1.797GiB   9.27%               0B / 0B             0B / 0B             18
# 测试
[root@VM_0_10_centos bin]# curl 'localhost:9200'
{
  "name" : "192b61492fb7",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "aZNiXOmTR9ak6u8Bu8Ogyg",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
~~~

### 可视化

> Portainer
>
> ~~~shell
> docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
> ~~~
>
> Rancher（CI/CD 再用）

#### Portainer?

Docker图形化界面管理工具，提供一个后台面板供我们操作。

~~~shell
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
~~~

访问外网8088就可以了。

### Docker镜像讲解

#### 镜像是什么

#### Docker镜像加载原理

#### 分层理解

#### commit镜像

~~~ shell
docker commit 提交容器成为一个新的镜像
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名[:tag]
~~~

### 容器数据卷

如果数据都在容器中，那么我们容器删除，数据就会丢失！比如MySql，容器删了就等于删库了。

容器的持久化和同步操作！容器间也是可以数据共享的！

##### 使用数据卷

> 方式一：直接使用命令来挂载 -v

~~~shell
docker run -it -v 主机目录:容器内目录

# 测试
[root@VM_0_10_centos ~]# docker run -it -v /home/dockerv:/home/dockerv centos /bin/bash
# 测试文件的同步
[root@979034c95228 /]# cd /home/
[root@979034c95228 home]# ls
dockerv
[root@979034c95228 home]# cd dockerv/
[root@979034c95228 dockerv]# ls
[root@979034c95228 dockerv]# touch test.java		# 在容器路径下创建test.java
[root@979034c95228 dockerv]# [root@VM_0_10_centos ~]# cd /home/dockerv/
[root@VM_0_10_centos dockerv]# ls		# 查看宿主主机挂载的目录下已经同步
test.java
# 同样反向的操作也是生效的，是双向绑定的！
~~~

### DockerFile

### Docker 网络

