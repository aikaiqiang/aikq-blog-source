---
title: Docker+Rancher+Docker registry服务镜像化
date: 2018-09-30 16:00:54
tags:
   - Docker
   - Rancher
   - Docker Registry
categories:
   - Docker
---

（本次操作基于CentOS 7部署）

### 安装Docker

- 利用yum工具安装docker

  ```shell
  yum -y install docker
  ```

- 启动docker

  ```
  systemctl start docker
  ```

  输入上面命令后，报错如图:{% asset_img 1_start_error.png 启动报错 %}

  根据提示输入命令：

  ```
  systemctl status docker.service
  ```

  输出信息如下：

  {% asset_img 2_status_docker.png 启动报错2 %}

  输出指出：SELinux 不支持overlay2，所以要关闭SELinux，编辑文件 /etc/sysconfig/docker  ：

  `vi /etc/sysconfig/docker`

  {% asset_img 3_edit_docker_config.png 修改配置docker文件 %}

  重启docker： `systemctl restart docker`

- 查看docker服务状态

  ```
  systemctl status docker
  ```
  {% asset_img 4_docker_status.png 查看docker服务状态 %}

  到此docker已经安装并成功启动。。。

  -----

- 将docker服务加入开启自启动

  ```
   systemctl enable docker
  ```

- 修改docker镜像地址为国内官方地址，重启docker是配置生效；

  `vim /etc/docker/daemon.json`

  ```
  {
   "registry-mirrors": ["https://registry.docker-cn.com"]
  }
  ```

- 修改docker镜像和容器的存储位置，Docker默认的镜像和容器存储位置在/var/lib/docker ：

  {% asset_img 5_docker_info.png 查看docker信息 %}

  编辑文件docker.service，使用-g参数指定存储位置

  ```
  vi /usr/lib/systemd/system/docker.service
  ```
  ExecStart=/usr/bin/dockerd下面添加如下内容 :

  --graph  /data/tool/docker  #自定义存储目录

 {% asset_img docker_image_location.png docker镜像自定义目录 %}

  reload配置文件：systemctl daemon-reload

  重启docker服务：systemctl restart docker

- 查看linux信息

  uname -a                    #查看linux当前操作系统内核信息

  cat /proc/version      #Linux查看当前操作系统版本信息

  cat /proc/cpuinfo      #Linux查看cpu相关信息，包括型号、主频、内核信息等

###  安装Rancher

1. docker容器化部署：（[参考连接](https://rancher.com/docs/rancher/v1.6/zh/installing-rancher/installing-server/)）

   - ##### 启动 RANCHER SERVER - 单容器部署 - 使用外部数据库：

     ```
     docker run -d --restart=unless-stopped -p 8080:8080 rancher/server \
          --db-host 192.168.0.14 --db-port 3306 --db-user root -db-password         Cobbler1234! --db-name cattle
     ```

     cattle 是提前在192.168.0.14上建好的数据库；

     - 启动报错：exec: "docker-proxy": executable file not found in $PATH 如图：

        {% asset_img 7_docker_run_error.png %}

     ​	查看下 docker-proxy 的位置： `cat /usr/lib/systemd/system/docker.service | grep prox`

     ​	创建一条软连接到 /usr/bin/ 下： `ln -s /usr/libexec/docker/docker-proxy-current /usr/bin/docker-proxy`

     - 启动报错2：Error response from daemon: shim error: docker-runc not installed on system，如图：

       {% asset_img 9_docker_run_error.png %}

       查看下 docker-proxy 的位置： `cat /usr/lib/systemd/system/docker.service | grep runc`

       创建一条软连接到 /usr/bin/ 下： `ln -s /usr/libexec/docker/docker-runc-current /usr/bin/docker-runc`

   - ##### 启动 RANCHER SERVER - 单容器部署 (NON-HA)

     ```
     docker run -d --restart=unless-stopped -p 8080:8080 rancher/server
     ```

     - 启动报错：iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 8080 -j DNAT --to-destination 172.17.0.2:8080 ! -i docker0: iptables: No chain/target/match by that name.如图：

       {% asset_img 10_docker_run_error.png %}

       处理报错：

       ​	1、先看能不能ping通网络。若能依次执行以下命令；

       　　2、pkill docker

       　　3、iptables -t nat -F

       　　4、ifconfig docker0 down

       　　5、brctl delbr docker0

       　　6、docker -d

       　　7、systmctl restart docker

       　　8、重启docker服务

       重启后，查看docker 容器： `docker ps -a`  如图：

       {% asset_img 11_docker_run_success.png %}


       启动成功，访问http://192.168.0.93:8080（部署服务器地址:端口）,如图：

       {% asset_img 12_docker_manage_index.png 管理界面 %}

   2. 配置RANCHER

      - 访问控制：

        {% asset_img 13_manage_controll.png 访问控制 %}

        目前选择本地身份认证（[参考链接](https://rancher.com/docs/rancher/v1.6/zh/configuration/access-control/#section-3)）：

        账户/密码：admin/123456

      - 添加主机

        {% asset_img 13_manage_add_host.png 添加主机 %}

   3.

### 部署私有仓库 Docker Registry

​	1.
