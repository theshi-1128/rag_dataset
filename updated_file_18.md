# 快速上手

> 此文档主要用于新用户快速上手，其中包含了 TuGraph 的简介、特征、安装和使用。

## 1.简介

TuGraph 是蚂蚁集团自主研发的大规模图计算系统，提供图数据库引擎和图分析引擎。其主要特点是大数据量存储和计算，高吞吐率，以及灵活的 API，同时支持高效的在线事务处理（OLTP）和在线分析处理（OLAP）。 LightGraph、GeaGraph 是 TuGraph 的曾用名。

主要功能特征包括：

- 标签属性图模型
- 支持多图
- 完善的 ACID 事务处理
- 内置 34 图分析算法
- 基于 web 客户端的图可视化工具
- 支持 RESTful API 和 RPC
- OpenCypher 图查询语言
- 基于 C++/Python 的存储过程
- 适用于高效图算法开发的 Traversal API

性能及可扩展性特征包括：

- TB 级大容量
- 千万点/秒的高吞吐率
- 高可用性支持
- 高性能批量导入
- 在线/离线备份


### 1.1.支持的平台

TuGraph 无论是物理、虚拟还是容器化环境，均支持 X86_64 和 ARM64 架构的的平台。

### 1.2.硬件要求

_目前我们建议用户使用 NVMe SSD 配合较大的内存配置以获取最佳性能。_

| 硬件   | 最低配置      | 建议配置                     |
|------|-----------|--------------------------|
| CPU  | X86_64    | Xeon E5 2670 v4          |
| 内存   | 4GB       | 256GB                    |
| 硬盘   | 100GB     | 1TB NVMe SSD             |
| 操作系统 | Linux 2.6 | Ubuntu 18.04, CentOS 7.3 |
表格内容描述:
该表格包含了三列，分别是“硬件”、“最低配置”和“建议配置”。 

- 在“硬件”这一行，CPU的最低配置为X86_64，建议配置为Xeon E5 2670 v4。
- 在“内存”这一行，最低配置为4GB，建议配置为256GB。
- 在“硬盘”这一行，最低配置为100GB，建议配置为1TB NVMe SSD。
- 在“操作系统”这一行，最低配置为Linux 2.6，建议配置为Ubuntu 18.04或CentOS 7.3。

总体来看，该表格展示了不同硬件的最低和建议配置要求，强调了在性能和存储方面的巨大提升建议。

## 2.安装

TuGraph 可以通过 Docker Image 快速安装，或者通过 rpm/deb 包本地安装。另外TuGraph在阿里云计算巢上提供了社区版服务，您无需自行购置云主机，即可在计算巢上快速部署TuGraph服务、实现运维监控，从而搭建您自己的图应用。

> 安装包/镜像下载：参考TuGraph最新版本：


| 描述                  | 文件                                         | 链接                                                                                                                                                                                              |
|---------------------|--------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CentOS7 安装包         | tugraph-4.5.0-1.el7.x86_64.rpm             | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-4.5.0-1.el7.x86_64.rpm)                                                                                      |
| CentOS8 安装包         | tugraph-4.5.0-1.el8.x86_64.rpm             | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-4.5.0-1.el8.x86_64.rpm)                                                                                      |
| Ubuntu18.04 安装包     | tugraph-4.5.0-1.x86_64.deb                 | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-4.5.0-1.x86_64.deb)                                                                                          |
| CentOS7 预安装镜像       | tugraph-runtime-centos7-4.5.0.tar          | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-runtime-centos7-4.5.0.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-runtime-centos7)                   |
| CentOS8 预安装镜像       | tugraph-runtime-centos8-4.5.0.tar          | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-runtime-centos8-4.5.0.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-runtime-centos8)                   |
| Ubuntu18.04 预安装镜像   | tugraph-runtime-ubuntu18.04-4.5.0.tar      | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-runtime-ubuntu18.04-4.5.0.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-runtime-ubuntu18.04)           |
| CentOS7 精简安装包       | tugraph-mini-4.5.0-1.el7.x86_64.rpm        | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-4.5.0-1.el7.x86_64.rpm)                                                                                 |
| CentOS8 精简安装包       | tugraph-mini-4.5.0-1.el8.x86_64.rpm        | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-4.5.0-1.el8.x86_64.rpm)                                                                                 |
| Ubuntu18.04 精简安装包   | tugraph-mini-4.5.0-1.x86_64.deb            | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-4.5.0-1.x86_64.deb)                                                                                     |
| CentOS7 精简预安装镜像     | tugraph-mini-runtime-centos7-4.5.0.tar     | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-runtime-centos7-4.5.0.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-mini-runtime-centos7)         |
| CentOS8 精简预安装镜像     | tugraph-mini-runtime-centos8-4.5.0.tar     | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-runtime-centos8-4.5.0.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-mini-runtime-centos8)         |
| Ubuntu18.04 精简预安装镜像 | tugraph-mini-runtime-ubuntu18.04-4.5.0.tar | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-runtime-ubuntu18.04-4.5.0.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-mini-runtime-ubuntu18.04) |
| CentOS7 编译镜像        | tugraph-compile-centos7-1.3.2.tar          | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-docker-compile/tugraph-compile-centos7-1.3.2.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-compile-centos7)          |
| CentOS8 编译镜像        | tugraph-compile-centos8-1.3.2.tar          | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-docker-compile/tugraph-compile-centos8-1.3.2.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-compile-centos8)          |
| Ubuntu18.04 编译镜像    | tugraph-compile-ubuntu18.04-1.3.2.tar      | [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-docker-compile/tugraph-compile-ubuntu18.04-1.3.2.tar) 、[访问](https://hub.docker.com/r/tugraph/tugraph-compile-ubuntu18.04)  |
表格内容描述:

该表格包含三列：描述、文件和链接。描述列提供了每种软件包或镜像的类型和操作系统，文件列则列出了对应的文件名，而链接列则提供了通往这些文件的下载地址及部分访问链接。

逐行描述如下：
1. CentOS7 安装包：文件名为 tugraph-4.5.0-1.el7.x86_64.rpm，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-4.5.0-1.el7.x86_64.rpm)。
2. CentOS8 安装包：文件名为 tugraph-4.5.0-1.el8.x86_64.rpm，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-4.5.0-1.el8.x86_64.rpm)。
3. Ubuntu18.04 安装包：文件名为 tugraph-4.5.0-1.x86_64.deb，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-4.5.0-1.x86_64.deb)。
4. CentOS7 预安装镜像：文件名为 tugraph-runtime-centos7-4.5.0.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-runtime-centos7-4.5.0.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-runtime-centos7)。
5. CentOS8 预安装镜像：文件名为 tugraph-runtime-centos8-4.5.0.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-runtime-centos8-4.5.0.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-runtime-centos8)。
6. Ubuntu18.04 预安装镜像：文件名为 tugraph-runtime-ubuntu18.04-4.5.0.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-runtime-ubuntu18.04-4.5.0.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-runtime-ubuntu18.04)。
7. CentOS7 精简安装包：文件名为 tugraph-mini-4.5.0-1.el7.x86_64.rpm，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-4.5.0-1.el7.x86_64.rpm)。
8. CentOS8 精简安装包：文件名为 tugraph-mini-4.5.0-1.el8.x86_64.rpm，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-4.5.0-1.el8.x86_64.rpm)。
9. Ubuntu18.04 精简安装包：文件名为 tugraph-mini-4.5.0-1.x86_64.deb，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-4.5.0-1.x86_64.deb)。
10. CentOS7 精简预安装镜像：文件名为 tugraph-mini-runtime-centos7-4.5.0.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-runtime-centos7-4.5.0.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-mini-runtime-centos7)。
11. CentOS8 精简预安装镜像：文件名为 tugraph-mini-runtime-centos8-4.5.0.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-runtime-centos8-4.5.0.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-mini-runtime-centos8)。
12. Ubuntu18.04 精简预安装镜像：文件名为 tugraph-mini-runtime-ubuntu18.04-4.5.0.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-4.5.0/tugraph-mini-runtime-ubuntu18.04-4.5.0.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-mini-runtime-ubuntu18.04)。
13. CentOS7 编译镜像：文件名为 tugraph-compile-centos7-1.3.2.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-docker-compile/tugraph-compile-centos7-1.3.2.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-compile-centos7)。
14. CentOS8 编译镜像：文件名为 tugraph-compile-centos8-1.3.2.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-docker-compile/tugraph-compile-centos8-1.3.2.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-compile-centos8)。
15. Ubuntu18.04 编译镜像：文件名为 tugraph-compile-ubuntu18.04-1.3.2.tar，下载链接为 [下载](https://tugraph-web.oss-cn-beijing.aliyuncs.com/tugraph/tugraph-docker-compile/tugraph-compile-ubuntu18.04-1.3.2.tar)，访问链接为 [访问](https://hub.docker.com/r/tugraph/tugraph-compile-ubuntu18.04)。

总结： 
该表格列出了针对不同操作系统（CentOS7、CentOS8 和 Ubuntu18.04）的多个软件包和预装镜像的详细信息，包括标准版、精简版及相应的编译镜像，每种类型都提供了对应的下载链接和访问链接，方便用户快速访问和下载所需的资源。



> 计算巢部署：可以在阿里云计算巢自行搜索，也可以通过部署链接(https://computenest.console.aliyun.com/user/cn-hangzhou/serviceInstanceCreate?ServiceId=service-7b50ea3d20e643da95bf&&isTrial=true)快速访问。

### 2.1.通过docker快速体验

1. 本地安装 docker 环境

   参考 docker 官方文档：https://docs.docker.com/get-started/

2. 拉取镜像
   ```shell
   docker pull tugraph/tugraph-runtime-centos7
   ```

3. 启动docker

启动 TuGraph 服务可以通过两种方式来实现。第一种方式将镜像拉取与服务启动整合在一起，用户只需执行运行容器的操作，即可同时启动 TuGraph 服务。第二种方式则是在创建 TuGraph 容器后，手动进入容器内部以触发服务启动。尽管这种方法初期步骤稍显繁琐，但在如忘记密码的情况下，它提供了更灵活的密码重置选项。

**方式一**

   ```shell
    docker run -d -p 7070:7070  -p 7687:7687 -p 9090:9090 -v /root/tugraph/data:/var/lib/lgraph/data  -v /root/tugraph/log:/var/log/lgraph_log \
    --name tugraph_demo ${REPOSITORY}:${VERSION}
   
   # ${REPOSITORY}是镜像地址，${VERSION}是版本号。
   # 7070是默认的http端口，访问tugraph-db-browser使用。   
   # 7687是bolt端口，bolt client访问使用。
   # 9090是默认的rpc端口，rpc client访问使用。
   # /var/lib/lgraph/data是容器内的默认数据目录，/var/log/lgraph_log是容器内的默认日志目录
   # 命令将数据目录和日志目录挂载到了宿主机的/root/tugraph/上进行持久化，您可以根据实际情况修改。
   ```

**方式二**

   ```shell
    docker run -dt -p 7070:7070  -p 7687:7687 -p 9090:9090 -v /root/tugraph/data:/var/lib/lgraph/data  -v /root/tugraph/log:/var/log/lgraph_log \
    --name tugraph_demo ${REPOSITORY}:${VERSION} /bin/bash
    
    docker exec -it tugraph_demo bash
    lgraph_server -c /usr/local/etc/lgraph.json -d start
    
   # ${REPOSITORY}是镜像地址，${VERSION}是版本号。
   # 7070是默认的http端口，访问tugraph-db-browser使用。   
   # 7687是bolt端口，bolt client访问使用。
   # 9090是默认的rpc端口，rpc client访问使用。
   # /var/lib/lgraph/data是容器内的默认数据目录，/var/log/lgraph_log是容器内的默认日志目录
   # 命令将数据目录和日志目录挂载到了宿主机的/root/tugraph/上进行持久化，您可以根据实际情况修改。
   ```

5. 前端访问

访问tugraph-db-browser: `http://x.x.x.x:7070`（7070 是 TuGraph Browser 的默认访问端口号），数据库地址格式为 `bolt://ip:bolt_port`（老版本不用填），默认用户名为 `admin`，密码为 `73@TuGraph`。
首次登录会默认跳转修改密码页面，请尽快修改默认密码避免安全风险。   

### 2.2.新旧前端说明
进入容器，可以通过修改配置文件"/usr/local/etc/lgraph.json"中的"web"参数来选择使用老版本或新版本的前端。对于老版本，可以将"web"的值设为"/usr/local/share/lgraph/resource"；对于新版本，可以将"web"的值设为"/usr/local/share/lgraph/browser-resource"。完成配置文件的修改后，请执行命令 `docker restart tugraph` 以使更改生效。需要注意的是，新版本是默认选项。
