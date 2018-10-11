---
title: 本地Docker镜像仓库Harbor搭建
date: 2018-10-11 15:40:50
tags:
   - Docker
   - Harbor
   - Docker Registry
categories:
   - Docker

---

### 关于Harbor

Harbor是一个用于存储和分发Docker镜像的企业级Registry服务器，通过添加一些企业必需的功能特性，例如安全、标识和管理等，扩展了开源Docker Distribution。作为一个企业级私有Registry服务器，Harbor提供了更好的性能和安全。提升用户使用Registry构建和运行环境传输镜像的效率。Harbor支持安装在多个Registry节点的镜像资源复制，镜像全部保存在私有Registry中， 确保数据和知识产权在公司内部网络中管控。另外，Harbor也提供了高级的安全特性，诸如用户管理，访问控制和活动审计等 ；



### 安装Harbor

安装环境：CentOS Linux release 7.2.1511 (Core )

#### 安装Docker

#### 安装Docker-Compose
- 二进制安装
- pip安装

#### 安装Harbor

##### 在线安装

###### 下载文件：
```
wget -P /usr/local/src/     https://github.com/vmware/harbor/releases/download/v1.2.0/harbor-online-installer-v1.2.0.tgz
```

######  解压文件：
```
tar zxf harbor-online-installer-v1.2.0.tgz  -C /usr/local/
```

###### 修改配置文件：
```
cd /usr/local/harbor/
vim harbor.cfg
# 域名设置
hostname = reg.hancloud.cn
# 邮箱设置
email_server = smtp.hancloud.cn
email_server_port = 25
email_username = aikaiqiang@hancloud.cn
email_password = @akq930210
email_from = aikaiqiang <aikaiqiang@hancloud.cn>
email_ssl = false
# 禁止用户注册
self_registration = off

# 设置只有管理员可以创建项目
project_creation_restriction = adminonly
```

###### 执行安装脚本：
```
./install.sh
# 执行过程
[Step 0]: checking installation environment ...

Note: docker version: 1.13.1

Note: docker-compose version: 1.22.0
      
[Step 1]: preparing environment ...
Generated and saved secret to file: /data/secretkey
Generated configuration file: ./common/config/nginx/nginx.conf
Generated configuration file: ./common/config/adminserver/env
Generated configuration file: ./common/config/ui/env
Generated configuration file: ./common/config/registry/config.yml
Generated configuration file: ./common/config/db/env
Generated configuration file: ./common/config/jobservice/env
Generated configuration file: ./common/config/jobservice/app.conf
Generated configuration file: ./common/config/ui/app.conf
Generated certificate, key file: ./common/config/ui/private_key.pem, cert file: ./common/config/registry/root.crt
The configuration files are ready, please use docker-compose to start the service.
  
[Step 2]: checking existing instance of Harbor ...
    
[Step 3]: starting Harbor ...
Creating network "harbor_harbor" with the default driver
Pulling log (vmware/harbor-log:v1.2.0)...
Trying to pull repository docker.io/vmware/harbor-log ... 
v1.2.0: Pulling from docker.io/vmware/harbor-log
93b3dcee11d6: Pull complete
07d028a1dbdd: Pull complete
208723cd598a: Pull complete
b876b05989fc: Pull complete
12f0e0ef448a: Pull complete
Digest: sha256:608e10b7aaac07e10a4cd639d8848aef96daa7dd5c7ebaaf6a7ecd47f903e1f8
Status: Downloaded newer image for docker.io/vmware/harbor-log:v1.2.0
Pulling registry (vmware/registry:2.6.2-photon)...
Trying to pull repository docker.io/vmware/registry ... 
2.6.2-photon: Pulling from docker.io/vmware/registry
4b40d82f242f: Pull complete
f01e70edcc2b: Pull complete
798027835c5f: Pull complete
8652f3177ad3: Pull complete
4753e541e7b5: Pull complete
058f3d3811f4: Pull complete
c6edb6ed847b: Pull complete
Digest: sha256:d7cdde865c1fc389900f1929d9c4865fd1b598ca9be3f27bbe900339a10a5788
Status: Downloaded newer image for docker.io/vmware/registry:2.6.2-photon
Pulling mysql (vmware/harbor-db:v1.2.0)...
Trying to pull repository docker.io/vmware/harbor-db ... 
v1.2.0: Pulling from docker.io/vmware/harbor-db
df559435c037: Pull complete
a6310a57af5d: Pull complete
d13d90890144: Pull complete
b694d8967a6c: Pull complete
a34f6cef56a6: Pull complete
3519eec83af5: Pull complete
34bae610e56c: Pull complete
86a867bebd89: Pull complete
3db2d0ac366e: Pull complete
c0d307ee295f: Pull complete
c5b1b404c5ee: Pull complete
14f4a4328366: Pull complete
c7fa77b46ec4: Pull complete
0cbe2b5669b8: Pull complete
Digest: sha256:c10b3555beb6d1c851ae49a4e90ef4296a1ad42bcd1a58ae97e316b034515b6e
Status: Downloaded newer image for docker.io/vmware/harbor-db:v1.2.0
Pulling adminserver (vmware/harbor-adminserver:v1.2.0)...
Trying to pull repository docker.io/vmware/harbor-adminserver ... 
v1.2.0: Pulling from docker.io/vmware/harbor-adminserver
93b3dcee11d6: Already exists
6ab21236e58b: Pull complete
f70b0efff900: Pull complete
c5d206b5e184: Pull complete
Digest: sha256:aba66ec90fc12fe0814cecc9f647f5d17b41395199821cc7af69db9c0fbe446c
Status: Downloaded newer image for docker.io/vmware/harbor-adminserver:v1.2.0
Pulling ui (vmware/harbor-ui:v1.2.0)...
Trying to pull repository docker.io/vmware/harbor-ui ... 
v1.2.0: Pulling from docker.io/vmware/harbor-ui
93b3dcee11d6: Already exists
6ab21236e58b: Already exists
7753f4b55df6: Pull complete
a647b33bdf74: Pull complete
eb30db926101: Pull complete
204d77847826: Pull complete
4910a0b56c59: Pull complete
e880a4b0031f: Pull complete
Digest: sha256:b198d8f2f59515d286bdcf06c7f99c370eb4475e2547495c3e1db8761940646b
Status: Downloaded newer image for docker.io/vmware/harbor-ui:v1.2.0
Pulling jobservice (vmware/harbor-jobservice:v1.2.0)...
Trying to pull repository docker.io/vmware/harbor-jobservice ... 
v1.2.0: Pulling from docker.io/vmware/harbor-jobservice
93b3dcee11d6: Already exists
6ab21236e58b: Already exists
64bd0172d071: Pull complete
b4f5382f226f: Pull complete
Digest: sha256:0692648176c1c87379025b0519036b9f3f1a0eceb2646f17dd40eb143c898d5c
Status: Downloaded newer image for docker.io/vmware/harbor-jobservice:v1.2.0
Pulling proxy (vmware/nginx-photon:1.11.13)...
Trying to pull repository docker.io/vmware/nginx-photon ... 
1.11.13: Pulling from docker.io/vmware/nginx-photon
4b40d82f242f: Already exists
2dcfe2cb4f13: Pull complete
Digest: sha256:5a32f122b86f2d7794651f1a67c5e9ce1dce79b0e0c1bc25d559405312c156db
Status: Downloaded newer image for docker.io/vmware/nginx-photon:1.11.13
Creating harbor-log ... done
Creating registry           ... done
Creating harbor-adminserver ... done
Creating harbor-db          ... done
Creating harbor-ui          ... done
Creating harbor-jobservice  ... done
Creating nginx              ... done

✔ ----Harbor has been installed and started successfully.----

Now you should be able to visit the admin portal at http://reg.hancloud.cn. 
For more details, please visit https://github.com/vmware/harbor 

```

执行成功！
```
# 查看本地镜像文件，会多处如图几个镜像
docker images
```
    {% asset_img harbor_images.png  %}
Harbor 管理是通过docker-compose来完成的，Harbor本身有多个服务进程，都放在docker容器之中运行，我们可以通过docker ps或者docker-compose  ps命令查看 ;
    {% asset_img harbor-docker-compose.png  %}
    
###### Harbor的启动和停止 ：
```
启动Harbor
# docker-compose start
停止Harbor
# docker-comose stop
重启Harbor
# docker-compose restart
```

###### 访问测试:
在浏览器输入 reg.hancloud.cn，harbor.cfg文件配置的域名为reg.hancloud.cn，默认账号/密码：admin/Harbor12345
       {% asset_img harbor-index.png %}


##### 上传下载镜像

###### 指定仓库地址

```
vim /etc/docker/daemon.json

{ "insecure-registries":["reg.hancloud.cn"] }
```

重启docker：`systemctl  restart docker` 

###### 创建Dockerfile

```
# vim Dockerfile 
FROM centos:centos7.1.1503
ENV TZ "Asia/Shanghai"
```

###### 创建镜像

```
# docker build -t reg.hanclound.cn/library/centos7.1:V0.0.1 .
```

###### 把镜像push到Harbor

```
# docker login reg.hanclound.cn
# docker push reg.hanclound.cn/library/centos7.1:V0.0.1
```

#### 配置TLS证书

#####  修改配置文件harbor.cfg 

```
hostname = 域名
ui_url_protocol = https
ssl_cert = /etc/certs/ca.crt
ssl_cert_key = /etc/certs/ca.key
```

##### 创建自签名证书key文件

```
# mkdir /etc/certs
# openssl genrsa -out /etc/certs/ca.key 2048 
```

##### 创建自签名证书crt文件
注意：命令中`/CN=reg.hanclound.cn `字段中`reg.hanclound.cn `修改为自己配置的仓库域名
```
# openssl req -x509 -new -nodes -key /etc/certs/ca.key -subj "/CN=rgs.unixfbi.com" -days 5000 -out /etc/certs/ca.crt
```

##### 安装Harbor

`./install.sh` 

##### 客户端配置
客户端需要创建证书文件存放的位置，并且把服务端创建的证书拷贝到该目录下，然后重启客户端docker。我们这里创建目录为：`/etc/docker/certs.d/reg.hanclound.cn ;

把服务端crt证书文件拷贝到客户端，例如我这的客户端为：192.168.0.94; 
`# scp /etc/certs/ca.crt root@192.168.0.94:/etc/docker/certs.d/reg.hanclound.cn/`
重启客户端docker 
`systemctl restart docker` 


参考文案：

博客：[运维特工](https://www.cnblogs.com/pangguoping/p/7650014.html)

