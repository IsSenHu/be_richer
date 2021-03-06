> Docker  学习Docker  学习Docker  学习Docker  学习Docker  学习Docker  学习Docker  学习Docker  学习Docker  学习Docker  学习
> sssssssssss
>
> Sssssssssss

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

  [TOC]

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

#### Portainer ？

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

#### 使用数据卷

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

#### 安装MySQL

~~~shell
# 获取镜像
[root@VM_0_10_centos ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
8559a31e96f4: Already exists 
d51ce1c2e575: Pull complete 
c2344adc4858: Pull complete 
fcf3ceff18fc: Pull complete 
16da0c38dc5b: Pull complete 
b905d1797e97: Pull complete 
4b50d1c6b05c: Pull complete 
d85174a87144: Pull complete 
a4ad33703fa8: Pull complete 
f7a5433ce20d: Pull complete 
3dcd2a278b4a: Pull complete 
Digest: sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
# 运行容器，需要做数据挂载
[root@VM_0_10_centos ~]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
1881ae2a6ce8f65a4c75c72991d5d645b0f064b4118a7a10b3a1c8733d3869d3
[root@VM_0_10_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
1881ae2a6ce8        mysql:5.7           "docker-entrypoint.s…"   4 seconds ago       Up 2 seconds        33060/tcp, 0.0.0.0:3310->3306/tcp   mysql01
# 然后就可以用工具进行连接测试
~~~

#### 具名和匿名挂载

~~~shell
# 匿名挂载
-v 容器内路径！
docker run -d -P --name nginx01 -v /etc/nginx nginx

# 查看所有的卷的情况
[root@VM_0_10_centos mysql]# docker volume list
DRIVER              VOLUME NAME
local               3fc2a56d351d24953347182c7fef3af9ae636c1ff83d18f0de0c29935e657877
local               879d7acacb6dd647c4ce44d092a48b1abba47fddfed44a00edaaa6e9c5a33bd0
local               7377f3e168c18e0bbb895ed4bcb8ba1d424abeb9e4c8a664f401b81db333176f
local               0573798a94de135d101d50029f95826e6309df31c2dc31098dcf5a570445867c
local               ac5fbdbfe263d1178b8c813aaa1f0a5bcf71037e0add427ef9dd5e611fa37409
local               d2bcd0097490d06ea7511d08ebf9b0492f682a3788e61727d655377aceff2f44
local               ea557af2d444bb65294c2cb67482fe3d161f117c37731536e1e2ce771900fd7e
local               fa7a8ab48c44e3648651f646a91c9e77006da7ba5256e80b7d9b6d4de3dbc605
# 上面这些没有起名字的都是匿名挂载，我在 -v 的时候只写了容器内的路径，没有写容器外的路径！

# 具名挂载
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx

# 查看所有的卷的情况
[root@VM_0_10_centos mysql]# docker volume ls
DRIVER              VOLUME NAME
local               3fc2a56d351d24953347182c7fef3af9ae636c1ff83d18f0de0c29935e657877
local               879d7acacb6dd647c4ce44d092a48b1abba47fddfed44a00edaaa6e9c5a33bd0
local               7377f3e168c18e0bbb895ed4bcb8ba1d424abeb9e4c8a664f401b81db333176f
local               0573798a94de135d101d50029f95826e6309df31c2dc31098dcf5a570445867c
local               ac5fbdbfe263d1178b8c813aaa1f0a5bcf71037e0add427ef9dd5e611fa37409
local               d2bcd0097490d06ea7511d08ebf9b0492f682a3788e61727d655377aceff2f44
local               ea557af2d444bb65294c2cb67482fe3d161f117c37731536e1e2ce771900fd7e
local               fa7a8ab48c44e3648651f646a91c9e77006da7ba5256e80b7d9b6d4de3dbc605
local               juming-nginx
# 上面juming-nginx就是具名挂载

# 查看挂载的信息
[root@VM_0_10_centos mysql]# docker inspect juming-nginx
[
    {
        "CreatedAt": "2020-07-02T10:03:30+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
# 进入挂载的目录
[root@VM_0_10_centos mysql]# cd /var/lib/docker/
[root@VM_0_10_centos docker]# cd volumes/
# 所有的挂载
[root@VM_0_10_centos volumes]# ll
drwxr-xr-x 3 root root  4096 1月  17 2019 0573798a94de135d101d50029f95826e6309df31c2dc31098dcf5a570445867c
drwxr-xr-x 3 root root  4096 7月   1 15:45 3fc2a56d351d24953347182c7fef3af9ae636c1ff83d18f0de0c29935e657877
drwxr-xr-x 3 root root  4096 1月  17 2019 7377f3e168c18e0bbb895ed4bcb8ba1d424abeb9e4c8a664f401b81db333176f
drwxr-xr-x 3 root root  4096 7月   2 09:58 879d7acacb6dd647c4ce44d092a48b1abba47fddfed44a00edaaa6e9c5a33bd0
drwxr-xr-x 3 root root  4096 1月  16 2019 ac5fbdbfe263d1178b8c813aaa1f0a5bcf71037e0add427ef9dd5e611fa37409
drwxr-xr-x 3 root root  4096 1月  16 2019 d2bcd0097490d06ea7511d08ebf9b0492f682a3788e61727d655377aceff2f44
drwxr-xr-x 3 root root  4096 1月  16 2019 ea557af2d444bb65294c2cb67482fe3d161f117c37731536e1e2ce771900fd7e
drwxr-xr-x 3 root root  4096 1月  16 2019 fa7a8ab48c44e3648651f646a91c9e77006da7ba5256e80b7d9b6d4de3dbc605
drwxr-xr-x 3 root root  4096 7月   2 10:03 juming-nginx
-rw------- 1 root root 65536 7月   2 10:03 metadata.db
[root@VM_0_10_centos volumes]# cd juming-nginx/
[root@VM_0_10_centos juming-nginx]# ll
总用量 4
drwxr-xr-x 3 root root 4096 7月   2 10:03 _data
[root@VM_0_10_centos juming-nginx]# cd _data/

# 看到挂载的nginx的路径都在这里
[root@VM_0_10_centos _data]# ll
总用量 40
drwxr-xr-x 2 root root 4096 7月   2 10:03 conf.d
-rw-r--r-- 1 root root 1007 5月  26 23:00 fastcgi_params
-rw-r--r-- 1 root root 2837 5月  26 23:00 koi-utf
-rw-r--r-- 1 root root 2223 5月  26 23:00 koi-win
-rw-r--r-- 1 root root 5231 5月  26 23:00 mime.types
lrwxrwxrwx 1 root root   22 5月  26 23:01 modules -> /usr/lib/nginx/modules
-rw-r--r-- 1 root root  643 5月  26 23:01 nginx.conf
-rw-r--r-- 1 root root  636 5月  26 23:00 scgi_params
-rw-r--r-- 1 root root  664 5月  26 23:00 uwsgi_params
-rw-r--r-- 1 root root 3610 5月  26 23:00 win-utf
~~~

我们通过具名挂载可以方便的找到我们的一个卷，大多数情况下使用具名挂载。

~~~shell
# -v 容器内路径:ro,rw 改变读写权限
ro		readonly	# 只读
rw		readwrite	# 可读可写
# 一旦设置容器权限，容器对我们挂载出来的内容就有限定了！
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx

# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内是无法操作的。
~~~

#### 数据卷容器

多个mysql同步数据！

~~~shell
# 启动3个容器，通过我们刚才自己写的镜像

# 启动docker01
[root@VM_0_10_centos docker-test-volume]# docker run -it --name docker01 husen/centos:1.0 /bin/bash

# 启动docker02
[root@2b98b24dfe2c /]# [root@VM_0_10_centos docker-test-volume]# docker run -it --name docker02 --volumes-from docker01 husen/centos:1.0
[root@0a6360dd1a07 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  `volume01`	`volume02`

# 进入docker01的数据卷创建一个docker01文件
[root@VM_0_10_centos docker-test-volume]# docker exec -it docker01 /bin/bash
[root@2b98b24dfe2c /]# cd volume01/
[root@2b98b24dfe2c volume01]# touch docker01
[root@2b98b24dfe2c volume01]# read escape sequence

# 进入docker02查看数据卷的内容已经同步了docker01的文件
[root@VM_0_10_centos docker-test-volume]# docker exec -it docker02 /bin/bash
[root@0a6360dd1a07 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  `volume01`	`volume02`
[root@0a6360dd1a07 /]# cd volume01/
[root@0a6360dd1a07 volume01]# ls
`docker01`
[root@0a6360dd1a07 volume01]# lsread escape sequence

# 创建docker03
[root@VM_0_10_centos docker-test-volume]# docker run -it --name docker03 --volumes-from docker01 husen/centos:1.0

# 查看数据卷内容已经同步docker01的内容
[root@9a6e3bcae26d /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  `volume01`	`volume02`
[root@9a6e3bcae26d /]# cd volume01/
[root@9a6e3bcae26d volume01]# ls
`docker01`

# 在docker03创建docker03文件
[root@9a6e3bcae26d volume01]# touch docker03
[root@9a6e3bcae26d volume01]# ls
`docker01`  `docker03`

# 进入docker01数据卷docker03也同步过来了
[root@9a6e3bcae26d volume01]# [root@VM_0_1docker exec -it docker01 /bin/bash
[root@2b98b24dfe2c /]# cd volume01/
[root@2b98b24dfe2c volume01]# ls
`docker01`  `docker03`

# 删除docker01，数据卷也是存在的
[root@VM_0_10_centos docker-test-volume]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
9a6e3bcae26d        husen/centos:1.0    "/bin/sh -c /bin/bash"   5 minutes ago       Up 5 minutes                            docker03
0a6360dd1a07        husen/centos:1.0    "/bin/sh -c /bin/bash"   9 minutes ago       Up 9 minutes                            docker02
2b98b24dfe2c        husen/centos:1.0    "/bin/bash"              11 minutes ago      Up 11 minutes                           docker01
[root@VM_0_10_centos docker-test-volume]# docker rm -f 2b98b24dfe2c
2b98b24dfe2c
[root@VM_0_10_centos docker-test-volume]# docker exec -it docker02 /bin/bash
[root@0a6360dd1a07 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  `volume01`	`volume02`
~~~

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。 但是一旦你持续化到了本地，这个时候，本地的数据不会删除！

### DockerFile

#### 初始DockerFile

> DockerFile 就是用来构建docker镜像的构建文件！命令脚本！ 
>
> 构建步骤：
>
> 1. 编写一个dockerfile文件
> 2. docker build构建成为一个镜像
> 3. docker run运行镜像
> 4. docker push发布镜像（DockerHub、阿里云镜像仓库）

~~~shell
# 查看我们编写的dockerfile的内容
[root@VM_0_10_centos docker-test-volume]# cat dockerfile 
FROM centos

VOLUME ["volume01", "volume02"]

CMD echo "------end------"

CMD /bin/bash
# 这里的每个命令都是镜像的一层

# 通过dockerfile进行构建
[root@VM_0_10_centos docker-test-volume]# docker build -f dockerfile -t husen/centos:1.0 .
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
 ---> 831691599b88
Step 2/4 : VOLUME ["volume01", "volume02"]
 ---> Running in eed46ba8a8c6
Removing intermediate container eed46ba8a8c6
 ---> 2499b2f9ae4a
Step 3/4 : CMD echo "------end------"
 ---> Running in 0c7bbf8c514c
Removing intermediate container 0c7bbf8c514c
 ---> 4ae9c33afe73
Step 4/4 : CMD /bin/bash
 ---> Running in 876d0897b3bd
Removing intermediate container 876d0897b3bd
 ---> 96cf2f19e4b
Successfully built 96cf2f19e4b4
Successfully tagged husen/centos:1.0

# 查看生成的镜像
[root@VM_0_10_centos docker-test-volume]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
husen/centos          1.0                 96cf2f19e4b4        16 seconds ago      215MB

# 启动自己写的容器
[root@VM_0_10_centos docker-test-volume]# docker run -it husen/centos:1.0 /bin/bash
[root@60bc89e074b4 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  `volume01`	`volume02`
# volume01和volume02卷和外部一定有一个同步的目录
[root@VM_0_10_centos docker-test-volume]# docker inspect 60bc89e074b4
"Mounts": [
            {
                "Type": "volume",
                "Name": "9a7f3daa1a7f02cc5ae2c3ca122566b05ecb677a0a15bee1a841de3e8537335b",
                "Source": "/var/lib/docker/volumes/9a7f3daa1a7f02cc5ae2c3ca122566b05ecb677a0a15bee1a841de3e8537335b/_data",
                "Destination": "volume01",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "9515c92675a736c9d33ea0e94dbb3242ca7dd8449407df980def399c5ed312d7",
                "Source": "/var/lib/docker/volumes/9515c92675a736c9d33ea0e94dbb3242ca7dd8449407df980def399c5ed312d7/_data",
                "Destination": "volume02",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ]
~~~

这种方式我们未来使用的十分多，因为我们通常会构建自己的镜像！

假设构建镜像的时候没有挂载卷，要手动镜像挂载 -v。

#### DockerFile构建步骤

##### 基础知识

1. 每个保留关键字（指令）都是必须是大写字母
2. 执行从上到下执行
3. #表示注释
4. 每一个指令都会创建提交一个新的镜像层并提交

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单！Docker逐渐成为了企业交付的标准，必须要掌握！

##### DockerFile的指令

~~~shell
FROM				# 基础镜像，一切从这里开始构建
MAINTAINER	# 镜像是谁写的，姓名 + 邮箱
RUN					# 镜像构建的时候需要运行的命令
ADD					# 步骤，tomcat镜像，这个tomcat压缩包！添加内容
WORKDIR			# 镜像的工作目录
VOLUME			# 挂载的目录
EXPOSE			# 指定暴露端口
CMD					# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT	# 指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD			# 当构建一个被继承 DockerFile 这个时候就会运行ONBUILD的指令，触发指令。
COPY				# 类似ADD，将我们文件拷贝到镜像中
ENV					# 构建的时候设置环境变量
~~~

##### 实战测试

```shell
# 编写dockerfile文件
[root@VM_0_10_centos docker]# cat mydockerfile-centos 
FROM centos
MAINTAINER husen<1178515826@qq.com>
ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "------end------"
CMD /bin/bash
# 通过文件构建对象
[root@VM_0_10_centos docker]# docker build -f mydockerfile-centos -t mycentos:0.1 .
Sending build context to Docker daemon  2.048kB
Step 1/10 : FROM centos
 ---> 831691599b88
Step 2/10 : MAINTAINER husen<1178515826@qq.com>
 ---> Running in ece6d9a55bc4
Removing intermediate container ece6d9a55bc4
 ---> 742cbb30b28b
Step 3/10 : ENV MYPATH /usr/local
 ---> Running in ce6a9e02b2bf
Removing intermediate container ce6a9e02b2bf
 ---> b35cf6c24dd4
Step 4/10 : WORKDIR $MYPATH
 ---> Running in f2490c08569b
Removing intermediate container f2490c08569b
 ---> 4010611bd507
Step 5/10 : RUN yum -y install vim
 ---> Running in 362505cfc1c8
CentOS-8 - AppStream                            258 kB/s | 5.8 MB     00:22    
CentOS-8 - Base                                 1.4 MB/s | 2.2 MB     00:01    
CentOS-8 - Extras                               6.9 kB/s | 6.7 kB     00:00    
Dependencies resolved.
================================================================================
 Package             Arch        Version                   Repository      Size
================================================================================
Installing:
 vim-enhanced        x86_64      2:8.0.1763-13.el8         AppStream      1.4 M
Installing dependencies:
 gpm-libs            x86_64      1.20.7-15.el8             AppStream       39 k
 vim-common          x86_64      2:8.0.1763-13.el8         AppStream      6.3 M
 vim-filesystem      noarch      2:8.0.1763-13.el8         AppStream       48 k
 which               x86_64      2.21-12.el8               BaseOS          49 k

Transaction Summary
================================================================================
Install  5 Packages

Total download size: 7.8 M
Installed size: 31 M
Downloading Packages:
(1/5): gpm-libs-1.20.7-15.el8.x86_64.rpm         94 kB/s |  39 kB     00:00    
(2/5): vim-filesystem-8.0.1763-13.el8.noarch.rp 119 kB/s |  48 kB     00:00    
(3/5): which-2.21-12.el8.x86_64.rpm             219 kB/s |  49 kB     00:00    
(4/5): vim-enhanced-8.0.1763-13.el8.x86_64.rpm  724 kB/s | 1.4 MB     00:01    
(5/5): vim-common-8.0.1763-13.el8.x86_64.rpm    1.0 MB/s | 6.3 MB     00:06    
--------------------------------------------------------------------------------
Total                                           1.0 MB/s | 7.8 MB     00:07     
warning: /var/cache/dnf/AppStream-02e86d1c976ab532/packages/gpm-libs-1.20.7-15.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
CentOS-8 - AppStream                            1.5 MB/s | 1.6 kB     00:00    
Importing GPG key 0x8483C65D:
 Userid     : "CentOS (CentOS Official Signing Key) <security@centos.org>"
 Fingerprint: 99DB 70FA E1D7 CE22 7FB6 4882 05B5 55B3 8483 C65D
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : which-2.21-12.el8.x86_64                               1/5 
  Installing       : vim-filesystem-2:8.0.1763-13.el8.noarch                2/5 
  Installing       : vim-common-2:8.0.1763-13.el8.x86_64                    3/5 
  Installing       : gpm-libs-1.20.7-15.el8.x86_64                          4/5 
  Running scriptlet: gpm-libs-1.20.7-15.el8.x86_64                          4/5 
  Installing       : vim-enhanced-2:8.0.1763-13.el8.x86_64                  5/5 
  Running scriptlet: vim-enhanced-2:8.0.1763-13.el8.x86_64                  5/5 
  Running scriptlet: vim-common-2:8.0.1763-13.el8.x86_64                    5/5 
  Verifying        : gpm-libs-1.20.7-15.el8.x86_64                          1/5 
  Verifying        : vim-common-2:8.0.1763-13.el8.x86_64                    2/5 
  Verifying        : vim-enhanced-2:8.0.1763-13.el8.x86_64                  3/5 
  Verifying        : vim-filesystem-2:8.0.1763-13.el8.noarch                4/5 
  Verifying        : which-2.21-12.el8.x86_64                               5/5 

Installed:
  gpm-libs-1.20.7-15.el8.x86_64         vim-common-2:8.0.1763-13.el8.x86_64    
  vim-enhanced-2:8.0.1763-13.el8.x86_64 vim-filesystem-2:8.0.1763-13.el8.noarch
  which-2.21-12.el8.x86_64             

Complete!
Removing intermediate container 362505cfc1c8
 ---> e4aa3f246434
Step 6/10 : RUN yum -y install net-tools
 ---> Running in 7caf7f02dfc8
Last metadata expiration check: 0:00:18 ago on Thu Jul  2 08:51:38 2020.
Dependencies resolved.
================================================================================
 Package         Architecture Version                        Repository    Size
================================================================================
Installing:
 net-tools       x86_64       2.0-0.51.20160912git.el8       BaseOS       323 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 323 k
Installed size: 1.0 M
Downloading Packages:
net-tools-2.0-0.51.20160912git.el8.x86_64.rpm   772 kB/s | 323 kB     00:00    
--------------------------------------------------------------------------------
Total                                           333 kB/s | 323 kB     00:00     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : net-tools-2.0-0.51.20160912git.el8.x86_64              1/1 
  Running scriptlet: net-tools-2.0-0.51.20160912git.el8.x86_64              1/1 
  Verifying        : net-tools-2.0-0.51.20160912git.el8.x86_64              1/1 

Installed:
  net-tools-2.0-0.51.20160912git.el8.x86_64                                     

Complete!
Removing intermediate container 7caf7f02dfc8
 ---> 01d5e238c103
Step 7/10 : EXPOSE 80
 ---> Running in 108acb6a42c4
Removing intermediate container 108acb6a42c4
 ---> a392c8288a85
Step 8/10 : CMD echo $MYPATH
 ---> Running in 986c2c608c3b
Removing intermediate container 986c2c608c3b
 ---> 875084edd452
Step 9/10 : CMD echo "------end------"
 ---> Running in 626c7940ef62
Removing intermediate container 626c7940ef62
 ---> 0b8ec769ca08
Step 10/10 : CMD /bin/bash
 ---> Running in 740b85ccb493
Removing intermediate container 740b85ccb493
 ---> 987ecc86638b
Successfully built 987ecc86638b
Successfully tagged mycentos:0.1
[root@VM_0_10_centos docker]# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED              SIZE
mycentos              0.1                 987ecc86638b        About a minute ago   287MB
husen/centos          1.0                 96cf2f19e4b4        6 hours ago          215MB
centos                latest              831691599b88        2 weeks ago          215MB
tomcat                9.0                 2eb5a120304e        3 weeks ago          647MB
tomcat                latest              2eb5a120304e        3 weeks ago          647MB
tomcat                latest              2eb5a120304e        3 weeks ago          647MB
nginx                 latest              2622e6cca7eb        3 weeks ago          132MB
mysql                 5.7                 9cfcce23593a        3 weeks ago          448MB
portainer/portainer   latest              cd645f5a4769        4 weeks ago          79.1MB
elasticsearch         7.6.2               f29a1ee41030        3 months ago         791MB
registry              latest              33fbbf4a24e5        18 months ago        24.2MB
busybox               latest              3a093384ac30        18 months ago        1.2MB
centos                latest              1e1148e4cc2c        19 months ago        202MB
training/webapp       latest              6fae60ef3446        5 years ago          349MB
training/postgres     latest              6fa973bb3c26        6 years ago          365MB
# 运行构建的镜像
[root@VM_0_10_centos docker]# docker run -it mycentos:0.1
[root@c8d767a91801 local]# pwd
`/usr/local`
# 发现进入进去就是设置的工作目录，vim和ifconfig也都支持了
[root@c8d767a91801 local]# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@c8d767a91801 local]# vim
# 我们可以列出本地进行的变更历史
[root@VM_0_10_centos docker]# docker history 987ecc86638b
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
987ecc86638b        5 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B                  
0b8ec769ca08        5 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B                  
875084edd452        5 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B                  
a392c8288a85        5 minutes ago       /bin/sh -c #(nop)  EXPOSE 80                    0B                  
01d5e238c103        5 minutes ago       /bin/sh -c yum -y install net-tools             14.3MB              
e4aa3f246434        5 minutes ago       /bin/sh -c yum -y install vim                   57.1MB              
4010611bd507        6 minutes ago       /bin/sh -c #(nop) WORKDIR /usr/local            0B                  
b35cf6c24dd4        6 minutes ago       /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B                  
742cbb30b28b        6 minutes ago       /bin/sh -c #(nop)  MAINTAINER husen<11785158…   0B                  
831691599b88        2 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B                  
<missing>           2 weeks ago         /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B                  
<missing>           2 weeks ago         /bin/sh -c #(nop) ADD file:84700c11fcc969ac0…   215MB   
# 我们平时拿到一个镜像可以研究一下它是怎么做的了
```

##### CMD、ENTRYPOINT

CMD

```shell
[root@VM_0_10_centos docker]# cat dockerfile-cmd-test 
FROM centos
CMD ["ls", "-a"]
[root@VM_0_10_centos docker]# docker build -f dockerfile-cmd-test -t cmdtest .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM centos
 ---> 831691599b88
Step 2/2 : CMD ["ls", "-a"]
 ---> Running in f23189f02423
Removing intermediate container f23189f02423
 ---> e4af0f7d9991
Successfully built e4af0f7d9991
Successfully tagged cmdtest:latest
[root@VM_0_10_centos docker]# docker run e4af0f7d9991
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

# CMD的情况下 -l 替换了CMD ["ls", "-a"] 命令，-l不是命令所以报错！
[root@VM_0_10_centos docker]# docker run e4af0f7d9991 -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-l\": executable file not found in $PATH": unknown.
[root@VM_0_10_centos docker]# docker run e4af0f7d9991  ls -al
total 56
drwxr-xr-x  1 root root 4096 Jul  2 09:07 .
drwxr-xr-x  1 root root 4096 Jul  2 09:07 ..
-rwxr-xr-x  1 root root    0 Jul  2 09:07 .dockerenv
lrwxrwxrwx  1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x  5 root root  340 Jul  2 09:07 dev
drwxr-xr-x  1 root root 4096 Jul  2 09:07 etc
drwxr-xr-x  2 root root 4096 May 11  2019 home
lrwxrwxrwx  1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx  1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------  2 root root 4096 Jun 11 02:35 lost+found
drwxr-xr-x  2 root root 4096 May 11  2019 media
drwxr-xr-x  2 root root 4096 May 11  2019 mnt
drwxr-xr-x  2 root root 4096 May 11  2019 opt
dr-xr-xr-x 99 root root    0 Jul  2 09:07 proc
dr-xr-x---  2 root root 4096 Jun 11 02:35 root
drwxr-xr-x 11 root root 4096 Jun 11 02:35 run
lrwxrwxrwx  1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x  2 root root 4096 May 11  2019 srv
dr-xr-xr-x 13 root root    0 Jul  2 09:07 sys
drwxrwxrwt  7 root root 4096 Jun 11 02:35 tmp
drwxr-xr-x 12 root root 4096 Jun 11 02:35 usr
drwxr-xr-x 20 root root 4096 Jun 11 02:35 var
```

ENTRYPOINT

~~~shell
[root@VM_0_10_centos docker]# cat dockerfile-entrypoint 
FROM centos
ENTRYPOINT ["ls", "-a"]
[root@VM_0_10_centos docker]# docker build -f dockerfile-entrypoint -t entrypoint .
Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM centos
 ---> 831691599b88
Step 2/2 : ENTRYPOINT ["ls", "-a"]
 ---> Running in db59219c0285
Removing intermediate container db59219c0285
 ---> ffa62bc7555c
Successfully built ffa62bc7555c
Successfully tagged entrypoint:latest
[root@VM_0_10_centos docker]# docker run ffa62bc7555c
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

# 我们追加命令是直接拼接在我们的ENTRYPOINT命令的后面
[root@VM_0_10_centos docker]# docker run ffa62bc7555c -l
total 56
drwxr-xr-x   1 root root 4096 Jul  2 09:10 .
drwxr-xr-x   1 root root 4096 Jul  2 09:10 ..
-rwxr-xr-x   1 root root    0 Jul  2 09:10 .dockerenv
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  340 Jul  2 09:10 dev
drwxr-xr-x   1 root root 4096 Jul  2 09:10 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Jun 11 02:35 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 102 root root    0 Jul  2 09:10 proc
dr-xr-x---   2 root root 4096 Jun 11 02:35 root
drwxr-xr-x  11 root root 4096 Jun 11 02:35 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  13 root root    0 Jul  2 09:10 sys
drwxrwxrwt   7 root root 4096 Jun 11 02:35 tmp
drwxr-xr-x  12 root root 4096 Jun 11 02:35 usr
drwxr-xr-x  20 root root 4096 Jun 11 02:35 var
~~~

##### Tomcat镜像

1. 准备镜像文件tomcat压缩包，jdk的压缩包
2. 编写dockerfile文件，官方命名Dockerfile，build会自动寻找这个文件，就不需要-f 指定了！

##### 发布镜像

> Dockerhub
>
> 阿里云镜像服务器

### Docker 网络

#### 理解Docker网络

清空所有环境

> 测试

~~~shell
[root@VM_0_10_centos tomcat]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo	# 本机回环地址
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:a2:81:bb brd ff:ff:ff:ff:ff:ff
    inet 172.27.0.10/20 brd 172.27.15.255 scope global eth0
       valid_lft forever preferred_lft forever	# 阿里云内网地址
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN 
    link/ether 02:42:6a:14:31:59 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0	# docker0地址
       valid_lft forever preferred_lft forever	
~~~

三个网络

~~~shell
# 启动一个tomcat
[root@VM_0_10_centos tomcat]# docker run -d -P --name tomcat01 tomcat
Unable to find image 'tomcat:latest' locally
latest: Pulling from library/tomcat
Digest: sha256:81c2a95e5b1b5867229d75255abe54928d505deb81c8ff8949b61fde1a5d30a1
Status: Downloaded newer image for tomcat:latest
0a1d00a9100d914e6cc5c8ce27a1949f2aace3534ac0c8fa1dfe4ea8be69f1a2

# 查看容器内部网络地址
[root@VM_0_10_centos tomcat]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
# 发现容器启动的时候会得到一个eth0@if75 ip地址，docker分配的！
74: eth0@if75: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
# 主机能ping通容器内部
[root@VM_0_10_centos tomcat]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.070 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.045 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.060 ms
~~~

1. 我们每启动一个docker容器，docker就会给容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0，桥接模式，使用的技术是evth-pair技术！再次测试ip addr。

~~~shell
[root@VM_0_10_centos tomcat]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:a2:81:bb brd ff:ff:ff:ff:ff:ff
    inet 172.27.0.10/20 brd 172.27.15.255 scope global eth0
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP 
    link/ether 02:42:6a:14:31:59 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
75: veth6191918@if74: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP 
    link/ether aa:46:d8:82:e4:35 brd ff:ff:ff:ff:ff:ff link-netnsid 0
~~~

2. 再启动一个容器测试

~~~shell
[root@VM_0_10_centos tomcat]# docker run -d -P --name tomcat02 tomcat
a259486739de6c24d31f7ad151d831f81ad58d3fc8d919cf67e0f3a393808d0c
[root@VM_0_10_centos tomcat]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:a2:81:bb brd ff:ff:ff:ff:ff:ff
    inet 172.27.0.10/20 brd 172.27.15.255 scope global eth0
       valid_lft forever preferred_lft forever
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP 
    link/ether 02:42:6a:14:31:59 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 scope global docker0
       valid_lft forever preferred_lft forever
75: veth6191918@if74: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP 
    link/ether aa:46:d8:82:e4:35 brd ff:ff:ff:ff:ff:ff link-netnsid 0
77: vethc61269c@if76: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP 
    link/ether fa:f6:1f:dc:c2:de brd ff:ff:ff:ff:ff:ff link-netnsid 1
~~~

我们发现这个容器带来的网卡，都是一对对的，evth-pair就是一对的虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连。

正因为有这个特性，evth-pair充当一个桥梁，连接各种虚拟网络设备的。

Openstac、Docker容器之间的连接，OVS的连接都是使用的evth-pair技术。

我们测试tomcat02是否能ping通tomcat01

~~~shell
[root@VM_0_10_centos ~]# docker exec -it tomcat02 ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.071 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.052 ms
^Z64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.066 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.060 ms
~~~

结论：tomcat01和tomcat02是公用的一个路由器，docker0。

 所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用IP

255.255.0.1/16 域。

Docker中的所有的网络接口都是虚拟的，虚拟的转发效率高！（内网传递文件！）

> 如何通过名字来访问容器？

#### --link

~~~shell
# 加上--link启动一个tomcat03
[root@VM_0_10_centos ~]# docker run -d -P --name tomcat03 --link tomcat02 tomcat
61003d550a5f48ce05fb2c06778bbc600870d4e798983f10047bfce3677e798c
# 通过名字来ping通了
[root@VM_0_10_centos ~]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.072 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.057 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=3 ttl=64 time=0.066 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=4 ttl=64 time=0.066 ms

# 反向的不能ping通
[root@VM_0_10_centos ~]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known

# tomcat03的hosts配置
[root@VM_0_10_centos ~]# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
`172.17.0.3	tomcat02 037ae3a5c9a7`
172.17.0.4	61003d550a5f
# 不建议使用--link
~~~

#### 自定义网络

#####  网络模式

bridge：桥接

none：不配置网络

host：和宿主机共享网络

container：容器网络连通

##### 测试

~~~shell
# 默认--net bridge，而这个就是我们的docker0
docker run -d -P --name tomcat01 --net bridge tomcat

# docker0特点，默认，域名不能访问

# 我们可以自定义一个网络
[root@VM_0_10_centos ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
92e127b5c6e8b466c1aa8e7361ea77fa1f707528e2ceff2867a665d6b1cbcb75
[root@VM_0_10_centos ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
1cc457aee8bb        bridge              bridge              local
160bb725ebe3        host                host                local
92e127b5c6e8        `mynet`               bridge              local
# 启动两个tomcat指定网络
[root@VM_0_10_centos ~]# docker run -d -P --name tomcat-net-01 --net mynet tomcat
c1ac99a442b896a19421107e3198e6f9810a74e4e510a42163d62d9ff0b2f53c
[root@VM_0_10_centos ~]# docker run -d -P --name tomcat-net-02 --net mynet tomcat
94bd073e121fa0f941bc53d2551de6204f70f61d04249087d2b1df468edc9af4
[root@VM_0_10_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
94bd073e121f        tomcat              "catalina.sh run"   3 seconds ago       Up 2 seconds        0.0.0.0:10008->8080/tcp   tomcat-net-02
c1ac99a442b8        tomcat              "catalina.sh run"   8 seconds ago       Up 6 seconds        0.0.0.0:10007->8080/tcp   tomcat-net-01
# 查看网络信息
[root@VM_0_10_centos ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "92e127b5c6e8b466c1aa8e7361ea77fa1f707528e2ceff2867a665d6b1cbcb75",
        "Created": "2020-07-03T15:15:31.285245101+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "94bd073e121fa0f941bc53d2551de6204f70f61d04249087d2b1df468edc9af4": {
                "Name": "tomcat-net-02",
                "EndpointID": "2e2c53339b45d0372d57703765ea36bd0808a81d7e8ad53de55db13235f08a65",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "c1ac99a442b896a19421107e3198e6f9810a74e4e510a42163d62d9ff0b2f53c": {
                "Name": "tomcat-net-01",
                "EndpointID": "76a5525e2911045130aa4bf181b0d16005e3c136d4cf7f17cb9149b3ea8e2e95",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
# 通过ip和容器名都能ping通
[root@VM_0_10_centos ~]# docker exec tomcat-net-01 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.073 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.063 ms
64 bytes from 192.168.0.3: icmp_seq=3 ttl=64 time=0.062 ms
[root@VM_0_10_centos ~]# docker exec tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.034 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.056 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.045 ms
# 我们自定义的网络都已经帮我们维护好了对应的关系，推荐我们平时这样使用网络！
~~~

##### 网络联通

~~~shell
# 测试打通tomcat01 - mynet
[root@VM_0_10_centos ~]# docker network connect mynet tomcat01
[root@VM_0_10_centos ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "92e127b5c6e8b466c1aa8e7361ea77fa1f707528e2ceff2867a665d6b1cbcb75",
        "Created": "2020-07-03T15:15:31.285245101+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "77b5e250e9e2657d46ecb4bfdf69b3fd11acd7ce8e33a375a8cc80a6f244ff31": {
                "Name": "tomcat01",
                "EndpointID": "41b5cec373741df48fa876be24c6bb2b2ef49b0ff51abbba9c515f8758a2fd46",
                "MacAddress": "02:42:c0:a8:00:04",
                "IPv4Address": "192.168.0.4/16",
                "IPv6Address": ""
            },
            "94bd073e121fa0f941bc53d2551de6204f70f61d04249087d2b1df468edc9af4": {
                "Name": "tomcat-net-02",
                "EndpointID": "2e2c53339b45d0372d57703765ea36bd0808a81d7e8ad53de55db13235f08a65",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "c1ac99a442b896a19421107e3198e6f9810a74e4e510a42163d62d9ff0b2f53c": {
                "Name": "tomcat-net-01",
                "EndpointID": "76a5525e2911045130aa4bf181b0d16005e3c136d4cf7f17cb9149b3ea8e2e95",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
# 联通之后就是将tomcat01放到了mynet网络下
# 一个容器两个ip地址！阿里云服务，公网ip，内网IP。

# 已经可以ping通了
[root@VM_0_10_centos ~]# docker exec -it tomcat01 ping tomcat-net-01
PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.074 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.072 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=3 ttl=64 time=0.051 ms
~~~

#### 部署redis集群

> 分片 + 高可用 + 负载均衡

~~~shell
# 运行脚本
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

# 启动6个redis
docker run -p 6371:6379 -p 16371:16379 --name redis-1 -v /mydata/redis/node-1/data:/data -v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.11 redis redis-server /etc/redis/redis.conf

docker run -p 6372:6379 -p 16372:16379 --name redis-2 -v /mydata/redis/node-2/data:/data -v /mydata/redis/node-2/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.12 redis redis-server /etc/redis/redis.conf

docker run -p 6373:6379 -p 16373:16379 --name redis-3 -v /mydata/redis/node-3/data:/data -v /mydata/redis/node-3/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.13 redis redis-server /etc/redis/redis.conf

docker run -p 6374:6379 -p 16374:16379 --name redis-4 -v /mydata/redis/node-4/data:/data -v /mydata/redis/node-4/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.14 redis redis-server /etc/redis/redis.conf

docker run -p 6375:6379 -p 16375:16379 --name redis-5 -v /mydata/redis/node-5/data:/data -v /mydata/redis/node-5/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.15 redis redis-server /etc/redis/redis.conf

docker run -p 6376:6379 -p 16376:16379 --name redis-6 -v /mydata/redis/node-6/data:/data -v /mydata/redis/node-6/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.16 redis redis-server /etc/redis/redis.conf

# 已经启动好了6个redis了
[root@VM_0_10_centos conf]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                              NAMES
bdfca609c7cc        redis               "docker-entrypoint.s…"   4 seconds ago       Up 2 seconds        0.0.0.0:6376->6379/tcp, 0.0.0.0:16376->16379/tcp   redis-6
11e811f822fb        redis               "docker-entrypoint.s…"   15 seconds ago      Up 14 seconds       0.0.0.0:6375->6379/tcp, 0.0.0.0:16375->16379/tcp   redis-5
4605bd42badf        redis               "docker-entrypoint.s…"   21 seconds ago      Up 20 seconds       0.0.0.0:6374->6379/tcp, 0.0.0.0:16374->16379/tcp   redis-4
158a99c7eabc        redis               "docker-entrypoint.s…"   26 seconds ago      Up 24 seconds       0.0.0.0:6373->6379/tcp, 0.0.0.0:16373->16379/tcp   redis-3
4842c53e216f        redis               "docker-entrypoint.s…"   31 seconds ago      Up 30 seconds       0.0.0.0:6372->6379/tcp, 0.0.0.0:16372->16379/tcp   redis-2
e50e320c7b2d        redis               "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes        0.0.0.0:6371->6379/tcp, 0.0.0.0:16371->16379/tcp   redis-1

# 创建集群
[root@VM_0_1: not foundnf]# docker exec -it redis-1 /bin/sh
# ls
# pendonly.aof	nodes.conf
# 
# redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1 
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
M: e91ca6338ee8eb2beb2bcb718c09660c733ac99d 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
M: 249601e697acc5a9fcecbe121ad3fc4e93a8184e 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: 29e4e11aa64b7ff4d890a49613288e36671d4763 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: 87fdf487a159de75d651c350e0849bdf3728d178 172.38.0.14:6379
   replicates 29e4e11aa64b7ff4d890a49613288e36671d4763
S: e37583894d505537cebe934e2fc998e623b1683b 172.38.0.15:6379
   replicates e91ca6338ee8eb2beb2bcb718c09660c733ac99d
S: 6b2a8523c47b4034e2a6cbc046699f82a430f0f3 172.38.0.16:6379
   replicates 249601e697acc5a9fcecbe121ad3fc4e93a8184e
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join

>>> Performing Cluster Check (using node 172.38.0.11:6379)
M: e91ca6338ee8eb2beb2bcb718c09660c733ac99d 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 87fdf487a159de75d651c350e0849bdf3728d178 172.38.0.14:6379
   slots: (0 slots) slave
   replicates 29e4e11aa64b7ff4d890a49613288e36671d4763
S: e37583894d505537cebe934e2fc998e623b1683b 172.38.0.15:6379
   slots: (0 slots) slave
   replicates e91ca6338ee8eb2beb2bcb718c09660c733ac99d
M: 29e4e11aa64b7ff4d890a49613288e36671d4763 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
M: 249601e697acc5a9fcecbe121ad3fc4e93a8184e 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 6b2a8523c47b4034e2a6cbc046699f82a430f0f3 172.38.0.16:6379
   slots: (0 slots) slave
   replicates 249601e697acc5a9fcecbe121ad3fc4e93a8184e
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

# 查看集群信息
# redis-cli -c
127.0.0.1:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:139
cluster_stats_messages_pong_sent:145
cluster_stats_messages_sent:284
cluster_stats_messages_ping_received:140
cluster_stats_messages_pong_received:139
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:284
127.0.0.1:6379> cluster nodes
87fdf487a159de75d651c350e0849bdf3728d178 172.38.0.14:6379@16379 slave 29e4e11aa64b7ff4d890a49613288e36671d4763 0 1593764512568 4 connected
e37583894d505537cebe934e2fc998e623b1683b 172.38.0.15:6379@16379 slave e91ca6338ee8eb2beb2bcb718c09660c733ac99d 0 1593764512568 5 connected
29e4e11aa64b7ff4d890a49613288e36671d4763 172.38.0.13:6379@16379 master - 0 1593764511165 3 connected 10923-16383
249601e697acc5a9fcecbe121ad3fc4e93a8184e 172.38.0.12:6379@16379 master - 0 1593764511065 2 connected 5461-10922
e91ca6338ee8eb2beb2bcb718c09660c733ac99d 172.38.0.11:6379@16379 myself,master - 0 1593764511000 1 connected 0-5460
6b2a8523c47b4034e2a6cbc046699f82a430f0f3 172.38.0.16:6379@16379 slave 249601e697acc5a9fcecbe121ad3fc4e93a8184e 0 1593764512167 6 connected
~~~

### Spring微服务打包成docker镜像

### Docker Compose



### Docker Swarm

### CI/CD之Jenkins

- sss
  - 

1. 2222

- [x] Sssssss
  - [ ] X

$$
y = x + z
$$

|      |      |      |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |

[百度]: http://baidu.com

[^事事顺遂]: 万事如意

------

**sssss**

*sssss*

<u>sssssss</u>

~~ssssss~~

<!--sssssssssss-->

[百度](http://baidu.com)

![我](http://118.24.38.46:8848/nacos/img/logo-2000-390.svg)

