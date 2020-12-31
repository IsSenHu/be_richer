### Docker 安装

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

```shell
yum install -y yum-utils
```

```shell
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

```shell
yum makecache fast
```

```shell
yum -y install docker-ce
```

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://mpmxtd01.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

![截屏2020-11-19 下午5.25.01](https://studysssmd.oss-cn-chengdu.aliyuncs.com/typora/%E6%88%AA%E5%B1%8F2020-11-19%20%E4%B8%8B%E5%8D%885.25.01.png)