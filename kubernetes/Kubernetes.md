### Kubernetes

[TOC]

#### 发展经历

> Infrastructure as a service（阿里云）
>
> Platform as a service（新浪云）
>
> Software as a service（Office 365）

`MESOS`

`docker swarm`

`kubernetes`



#### 知识图谱

资源清单

Pod

Pod的生命周期

#### 组件说明

架构图

​	APISERVER

​	CrontrollerManager

​	Scheduler

​	ETCD

​	Kubelet

​	Kube-proxy

​	CoreDNS

​	DASHBOARD

​	INGRESS CONTROLLER

​	FEDETATION

​	PROMETHEUS

​	ELK

#### Pod的概念

- 自主式Pod
- 控制器管理的Pod

`ReplicationController`、`ReplicaSet`、`Deployment`

> ReplicationController用来确保容器应用的副本数始终保持在用户定义的副本数，即如果有容器异常退出，会自动创建新的Pod来替代；而如果异常多出来的容器也会自动回收。在新版本的Kubernetes中建议使用ReplicaSet来取代ReplicationController
>
> ReplicaSet跟ReplicationController没有本质的不同，只是名字不一样，并且ReplicaSet支持集合式的selector
>
> 虽然ReplicaSet可以独立使用，但一般还是建议使用Deployment来自动管理ReplicaSet，这样就无需担心跟其他几只的不兼容问题（ 比如ReplicaSet不支持rolling-update但Deployment支持）

`HPA`（HorizontalPodAutoScale）

> Horizontal Pod Autoscaling仅适用于Deployment和ReplicaSet，在V1版本中仅支持根据Pod的CPU利用率扩缩容，在vlalpha版本中，支持根据内存和用户自定义的metric扩缩容

`StateFullSet`

> StateFullSet是为了解决有状态服务的问题（对应Deployment和ReplicaSet是为无状态服务而设计），其应用场景包括：
>
> - 稳定的持久化存储，即Pod重新调度后还是能访问到相同的持久化数据，基于PVC来实现
> - 稳定的网络标志，即Pod重新调度后其PodName和HostName不变，基于Headless Service（即没有Cluster IP和Service）来实现
> - 有序部署，有序扩展，即Pod是有顺序的，在部署或者扩展的时候要依据定义的顺序依次进行（即从0到N-1，在下一个Pod运行之前所有的Pod必须都是Running和Ready状态），基于init containers来实现
> - 有序缩容，有序删除（即从N-1到0）

`DaemonSet`

> DaemonSet确保全部（或者一些）Node上运行一个Pod的副本。当有Node加入集群时，也会为他们新增一个Pod。当有Node从集群移除时，这些Pod也会被回收。删除DaemonSet将会删除它创建的所有Pod
>
> 使用Daemon的一些典型用法：
>
> - 运行集群存储daemon，例如在每个Node上运行glusterd、ceph。
> - 在每个Node上运行日志手机daemon，例如fluentd、logstash。
> - 在每个Node上运行监控daemon，例如Prometheus Node Exporter

`Job`、`Cronjob`

> Job负责批处理任务，即仅执行一次的任务，它保证批处理任务的一个或多个Pod成功结束
>
> Cron Job管理基于时间的job，即：
>
> - 再给点时间点只运行一次
> - 周期性地在给定时间点运行

#### 网络通讯的方式

Kubernetes的网络模型假定了所有Pod都在一个可以直接连通的扁平的网络空间，这在GCE（Google Compute Engine）里面是现场的网络模型，Kubernetes假定这个网络已经存在。而在私有云搭建Kubernetes集群，就不能假定这个网络已经存在了。我们需要自己实现这个网络假设，将不同节点上的Docker容器之间的互相访问先打通，然后运行Kubernetes。

Flannel是CoreOS团队针对Kubernetes设计的一个网络规划服务，简单来说，它的功能是让集群中的不同节点主机创建的Docker容器都具有全局唯一的虚拟IP地址，而且它还能再这些IP地址之间建立一个覆盖网络（Overlay Network），通过这个覆盖网络，将数据包原封不动地传递到目标容器内。

> EDCT之Flannel提供说明：
>
> - 存储管理Flannel可分配的IP地址段资源
> - 监控ETCD中每个Pod的实际地址，并在内存中建立维护Pod节点路由表

#### 集群安装

>cpu必须大于1核

##### 1. 首先是安装docker

参考之前写的文档安装即可

##### 2. 配置kubernetes仓库

```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

##### 3. 安装指定的组件

```shell
yum install -y kubelet
yum install -y kubeadm
yum install -y kubectl
```

##### 4. 禁用节点上的swap

```shell
swapoff -a 
sysctl -p
vim /etc/fstab
注释掉最后一行
reboot now
```

##### 5. 开启路由转发功能以及iptables的过滤策略

```shell
vim /etc/sysctl.d/k8s.conf

net.bridge.bridge-nf-call-ip6tables = 1
#开启iptables中ipv6的过滤规则
net.bridge.bridge-nf-call-iptables = 1
#开启ipv4的过滤规则
net.ipv4.ip_forward = 1
#开启服务器的路由转发功能

#执行命令使修改生效。
modprobe br_netfilter
sysctl -p /etc/sysctl.d/k8s.conf
```

##### 6. 机器通用配置

```shell
systemctl start docker.service
systemctl enable docker.service
systemctl enable kubelet.service
echo "1" >/proc/sys/net/bridge/bridge-nf-call-iptables
systemctl stop firewalld
systemctl disable firewalld.service

docker info | grep Cgrou
Cgroup Driver: cgroupfs

vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://mpmxtd01.mirror.aliyuncs.com"],
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  }
}
mkdir -p /etc/systemd/system/docker.service.d
systemctl daemon-reload
systemctl restart docker
docker info | grep Cgrou
Cgroup Driver: systemd
```

##### 7. 初始化master节点

```shell
#--kubernetes-version指明要下载的镜像版本
#--pod-network-cidr 指明网络的子网掩码，此处使用flannel模型的子网掩码
#--apiserver-advertise-address 指明与master结点绑定的ip

kubeadm init  --kubernetes-version=v1.20.0  --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=192.168.57.2 --apiserver-cert-extra-sans=192.168.57.2 | tee kubeadm-init.log
```

##### 8. 从节点加入

```shell
#和上面同样安装好k8s相关东西后执行下面的命令
kubeadm join 192.168.57.2:6443 --token 01icmr.3eqdkdghsz32cfgg \
    --discovery-token-ca-cert-hash sha256:9d152ff735d706db591683f4fb4d8ee430194b36ead6518150134f259906fb3c 
    
[preflight] Running pre-flight checks
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

##### 9. 让admin.conf生效

```shell
ll /etc/kubernetes/admin.conf
-rw-------. 1 root root 5568 12月 16 17:34 /etc/kubernetes/admin.conf
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bash_profile
source ~/.bash_profile

kubectl get nodes
NAME      STATUS     ROLES                  AGE   VERSION
server2   NotReady   control-plane,master   81m   v1.20.0
server3   NotReady   <none>                 57m   v1.20.0

kubectl get pods --all-namespaces
NAMESPACE     NAME                              READY   STATUS    RESTARTS   AGE
kube-system   coredns-74ff55c5b-7hhq5           0/1     Pending   0          80m
kube-system   coredns-74ff55c5b-8djs4           0/1     Pending   0          80m
kube-system   etcd-server2                      1/1     Running   0          80m
kube-system   kube-apiserver-server2            1/1     Running   0          80m
kube-system   kube-controller-manager-server2   1/1     Running   0          80m
kube-system   kube-proxy-6vp5m                  1/1     Running   0          80m
kube-system   kube-proxy-k97w7                  1/1     Running   0          57m
kube-system   kube-scheduler-server2            1/1     Running   0          80m
```

##### 10. 配置主节点是否参与调度

```shell
#master运行pod
kubectl taint nodes server2 node-role.kubernetes.io/master-

#master不运行pod
kubectl taint nodes server2 node-role.kubernetes.io/master=:NoSchedule
```

##### 11. 开启非安全端口访问

```shell
vim /etc/kubernetes/manifests/kube-apiserver.yaml

- --secure-port=6443
- --insecure-bind-address=0.0.0.0
- --insecure-port=8080
```

##### 12. 配置证书续期

```shell
- --kubeconfig=/etc/kubernetes/controller-manager.conf
- --experimental-cluster-signing-duration=87600h0m0s
- --feature-gates=RotateKubeletServerCertificate=true
```

##### 13. 使用flannel

```shell
mkdir -p /etc/cni/net.d/
cat <<EOF> /etc/cni/net.d/10-flannel.conf
{
  "name": "cbr0",
  "type": "flannel",
  "delegate": {
    "isDefaultGateway": true
  }
}
EOF
mkdir /usr/share/oci-umount/oci-umount.d -p
mkdir /run/flannel/
cat <<EOF> /run/flannel/subnet.env
FLANNEL_NETWORK=10.244.0.0/16
FLANNEL_SUBNET=10.244.1.0/24
FLANNEL_MTU=1450
FLANNEL_IPMASQ=true
EOF

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

flannel.1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1450
        inet 10.244.0.0  netmask 255.255.255.255  broadcast 10.244.0.0
        inet6 fe80::e82c:f4ff:fe03:86d7  prefixlen 64  scopeid 0x20<link>
        ether ea:2c:f4:03:86:d7  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 8 overruns 0  carrier 0  collisions 0
```

#### 配置私有仓库

> harbor

```shell
# 下载
wget https://studysssmd.oss-cn-chengdu.aliyuncs.com/harbor-offline-installer-v2.1.2.tgz

# 解压
tar -zxvf harbor-offline-installer-v2.1.2.tgz

# 进入解压后的目录
cd harbor
ls 
common.sh  harbor.v2.1.2.tar.gz  harbor.yml.tmpl  install.sh  LICENSE  prepare

# harbor.v2.1.2.tar.gz就是要用到的镜像
# 编辑配置文件harbor.yml
cp harbor.yml.tmpl harbor.yml
vim harbor.yml
# 修改 hostname 选项为你的域名或者ip
# 修改 ca相关文件所在的路径和下面配置https生成的路径保持一致即可
# 配置https
openssl genrsa -out ca.key 4096

openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=yourdomain.com" \
 -key ca.key \
 -out ca.crt
 
 openssl genrsa -out yourdomain.com.key 4096
 
 openssl req -sha512 -new \
    -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=yourdomain.com" \
    -key yourdomain.com.key \
    -out yourdomain.com.csr
 
cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=yourdomain.com
DNS.2=yourdomain
DNS.3=hostname
EOF

openssl x509 -req -sha512 -days 3650 \
    -extfile v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in yourdomain.com.csr \
    -out yourdomain.com.crt

cp yourdomain.com.crt /data/cert/
cp yourdomain.com.key /data/cert/

openssl x509 -inform PEM -in yourdomain.com.crt -out yourdomain.com.cert

cp yourdomain.com.cert /etc/docker/certs.d/yourdomain.com/
cp yourdomain.com.key /etc/docker/certs.d/yourdomain.com/
cp ca.crt /etc/docker/certs.d/yourdomain.com/

#配置docker
vim /etc/docker/daemon.json
"insecure-registries": ["https://yourdomain.com"]

#重启docker
systemctl restart docker

#准备
./prepare

#重启启动harbor
docker-compose up -d

#验证
docker login yourdomain.com -u admin
#并输入密码
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
#浏览器访问https://yourdomain.com测试是否能登陆成功
```

#### 最终的docker配置

> 每个节点都保持一样

```json
{
  "registry-mirrors": ["https://mpmxtd01.mirror.aliyuncs.com"],
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "insecure-registries": ["https://yourdomain.com"]
}
```

#### 试用一下kubernetes和私有仓库

```shell
#首先从网上拉取一个nginx镜像
docker pull nginx
#修改镜像名称
docker tag nginx yourdomain.com/library/nginx:v1
docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
nginx                          latest              ae2feff98a0c        35 hours ago        133MB
yourdomain.com/library/nginx   v1                  ae2feff98a0c        35 hours ago        133MB
#push到私有仓库
docker push yourdomain.com/library/nginx:v1
#然后就可以在仓库中看到了

#使用kubectl启动一个该容器
kubectl run nginx-deployment --image=yourdomain.com/library/nginx:v1 --port=80
#查看正在运行的容器信息
kubectl get pod -o wide
NAME               READY   STATUS    RESTARTS   AGE     IP           NODE      NOMINATED NODE   READINESS GATES
nginx-deployment   1/1     Running   0          3m48s   10.244.2.2   server4   <none>           <none>
#可以看到在节点server4上

curl '10.244.2.2'
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

#对副本进行均衡负载
kubectl expose pod nginx-deployment --port=30000 --target-port=80
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP           20h
nginx-deployment   ClusterIP    10.96.163.250   <none>        30000/TCP   27m
#就可以通过ip10.96.163.250负载请求到所有副本上了
#但是只能在主机内才能访问到，要想在外网能够访问必须修改一下svc的type为
kubectl edit svc nginx-deployment
#修改
type: NodePort

NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP           20h
nginx-deployment   NodePort    10.96.163.250   <none>        30000:30241/TCP   33m
#就可以在浏览器输入主机ip加端口号30241进行访问了
```

#### 启用ipvs

##### 1. 修改kube-proxy配置

```shell
kubectl edit configmap kube-proxy -n kube-system
mode: "ipvs"
#1、kube-proxy以configmap的方式存储
#2、如果让所有节点生效，需要重建所有节点kube-proxy pod
```

> 启用ipvs而不使用ipatables的原因
>
> - ipvs可以更快的重定向流量，并且在同步代理规则时具有更好的性能。此外ipvs算法提供了更多选项，例如：
> 	1. rr轮询调度
> 	2. lc最小连接数
> 	3. dh目标哈希
> 	4. sh源哈希
> 	5. sed最短期望延迟
> 	6. nq不排队调度

##### 2. 每个节点都需要进行如下操作

```shell
#安装ipvs相关软件包
yum -y install ipvsadm ipset
#修改内核参数
vim /etc/sysctl.conf 
#添加
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
#生效
sysctl -p
```

默认为空

```shell
ipvsadm -Ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
```

##### 3. 依次重启/删除kube-proxy的pod

> kubeadm不仅仅将k8s集群搭建简化了，还采用容器化的方式去部署k8s组件，这里只需要知道采用了容器化部署，因为帮我们封装好了。唯一一个采用的不是容器化部署的kubectl，这个使用的是传统的systemd去管理的，在宿主机进行管理。其他都是采用容器化部署，也就是通过启动容器帮你拉起组件。

```shell
kubectl get pod -n kube-system -o wide
NAME                              READY   STATUS    RESTARTS   AGE   IP             NODE      NOMINATED NODE   READINESS GATES
kube-proxy-c8lgw                  1/1     Running   0          17m   192.168.57.4   server4   <none>           <none>
kube-proxy-jxh25                  1/1     Running   0          17m   192.168.57.3   server3   <none>           <none>
kube-proxy-l6m7w                  1/1     Running   0          17m   192.168.57.2   server2   <none>           <none>
kube-scheduler-server2            1/1     Running   4          20h   192.168.57.2   server2   <none>           <none>
```

删除pod之后 会新启一个新的pod 

```shell
kubectl delete pod kube-proxy-bk57x -n kube-system
kubectl delete pod kube-proxy-gzl2r -n kube-system
kubectl delete pod kube-proxy-v6vm4 -n kube-system
```

等容器重启完毕再去看看

```shell
ipvsadm -Ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.96.0.1:443 rr
  -> 192.168.57.2:6443            Masq    1      0          0         
TCP  10.96.0.10:53 rr
  -> 10.244.0.8:53                Masq    1      0          0         
  -> 10.244.0.9:53                Masq    1      0          0         
TCP  10.96.0.10:9153 rr
  -> 10.244.0.8:9153              Masq    1      0          0         
  -> 10.244.0.9:9153              Masq    1      0          0         
TCP  10.96.163.250:30000 rr
  -> 10.244.2.5:80                Masq    1      0          0         
TCP  10.244.0.1:30241 rr
  -> 10.244.2.5:80                Masq    1      0          0         
TCP  127.0.0.1:30241 rr
  -> 10.244.2.5:80                Masq    1      0          0         
TCP  172.17.0.1:30241 rr
  -> 10.244.2.5:80                Masq    1      0          0         
TCP  172.18.0.1:30241 rr
  -> 10.244.2.5:80                Masq    1      0          0         
TCP  192.168.57.2:30241 rr
  -> 10.244.2.5:80                Masq    1      0          0         
TCP  10.244.0.0:30241 rr
  -> 10.244.2.5:80                Masq    1      0          0         
UDP  10.96.0.10:53 rr
  -> 10.244.0.8:53                Masq    1      0          0         
  -> 10.244.0.9:53                Masq    1      0          0         
```

### 资源清单

#### K8S中的资源

- 名称空间级别
	- 工作负载型资源
	- 服务发现及负载均衡型资源
	- 配置与存储型资源
	- 特殊类型的存储卷：ConfigMap、Secret
- 集群级别
	- Namespace、Node、Role、ClusterRole、RoleBinding、ClusterRoleBinding
- 元数据型
	- HPA、PodTemplate、LimitRange

#### 常用字段的解释

##### 必须存在的属性

- version String 这里指定的是K8S API的版本，可以用kubectl api-versions命令查询
- kind String 这里指的是yaml文件定义的资源类型和角色，比如：Pod
- metadata Object 元数据对象， 固定值就写metadata
- metadata name String 元数据对象的名字，这里由我们编写，比如命名Pod的名字
- metadata namespace String 元数据对象的命名空间，由我们自身定义
- spec Object 详细定义对象，固定值就写Spec
- spec.containers[] List 这里是Spec对象的容器列表定义，是个列表
- spec.containers[].name String 这里定义容器的名字
- spec.containers[].image String 这里定义要用到的镜像名称
- spec.containers[].imagePullPolicy String 定义镜像拉取策略，有Always、Never、IfNotPresent三个值可选
- spec containers[].command[] List 指定容器启动命令，因为数组可以指定多个，不指定则使用镜像打包时的启动命令
- spec.containerp[].args[] List 指定容器启动命令参数，因为是数组可以指定多个
- spec.containers[].workingDir String 指定容器的工作目录
- spec.containers[].volumeMounts[] List 指定容器内部的存储卷配置
- spec.containers[].volumeMounts[].name String 指定可以被容器挂载的存储卷的名称
- spec.containers[].volumeMounts[].mountPath String 指定可以被容器挂载的存储卷的路径
- spec.containers[].volumeMounts[].readOnly String 设置存储卷路径的读写模式，true或者false，默认为读写模式
- spec.containers[].ports[] List 指定容器需要用到的端口列表
- spec.containers[].ports[].name String 指定端口名称
- spec.containers[].ports[].containerPort String 指定容器需要监听的端口号
- spec.containers[].ports[].hostPort String 指定容器所在主机需要监听的端口号
- spec.containers[].ports[].protocol String 指定端口协议，支持TCP和UDP，默认值为TCP
- spec.containers[].env[] List 指定容器运行前设置的环境变量列表
- spec.containers[].env[].name String 指定环境变量名称
- spec.containers[].env[].value String 指定环境变量值
- spec.restartPolicy String 定义Pod的重启策略
- spec.nodeSelector Object 定义Node的Label过滤标签，以key:value格式指定
- spec.imagePullSecrets Object 定义Pull镜像时使用Secret名称，以name:secretKey格式指定
- spec.hostNetwork Boolean 定义是否使用主机网络模式，默认值为false。设置true表示使用宿主机网络，不使用docker网桥，同时设置了true将无法在同一台宿主机上启动第二个副本。

#### 示例yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod
  labels:
    app: myredis
spec:
  containers:
  - name: redis-app
    image: redis
```

然后输入命令启动

```shell
kubectl apply -f redis.yaml
```

输入名称查看Pod的运行状态

```shell
kubectl get pod
```

#### Pod的生命周期说明

