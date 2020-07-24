[![Build Status](https://travis-ci.com/willzhang/yumrepo.svg?branch=master)](https://travis-ci.com/willzhang/yumrepo)


## Yum Repo for Docker/K8S/NFS installation

usage1:

YUM Repo服务器安装好docker，直接运行命令：
```shell
docker run -d --restart always -p 2009:2009 --name=yum-repo willdockerhub/yum-repo:v1.16.3
```
在需要安装docker/k8s/nfs的其它主机上：

创建一个文件 docker.repo 并将其拷贝至 /etc/yum.repos.d/
```
cat > /etc/yum.repos.d/docker.repo <<EOF
[docker]
name=docker
baseurl=http://repo-server-ip:2009/rpms
enabled=1
gpgcheck=0
EOF
```

上面的 repo-server-ip 请写为上述服务器的真实IP地址

然后就可以直接用yum install命令命令安装相关软件了。例如：
```

yum install docker docker-python docker-compose

yum install rsync python-chardet jq nfs-utils
  
yum install kubernetes-cni kubectl kubelet kubeadm
```


## no docker

#online
```
docker run -d --restart always -p 2009:2009 --name=yum-repo willdockerhub/yum-repo:v1.0

docker cp yum-repo:/usr/share/nginx/html/rpms /root
tar -zcvf docker-rpms.tar.gz ./rpms


#offline
mkdir -p /data/rpms

#解压docker rpm包
cd /data/rpms
tar -zxvf docker-rpms.tar.gz

#创建本地yum源
cd /etc/yum.repos.d && tar -zcvf repo-bak.tar.gz * --remove-files
cat > /etc/yum.repos.d/docker-local.repo << EOF
[docker]
name=docker-local
baseurl=file:///data/rpms
gpgcheck=0
enabled=1
EOF
```
