# Class-05-dockerization

- [老师介绍](#----)
- [1.Contents](#1contents)
- [2.Docker basics](#2docker-basics)
  * [Devops cycle and our course](#devops-cycle-and-our-course)
  * [Docker introduction](#docker-introduction)
    + [What is Docker?](#what-is-docker-)
    + [Why Virtualisations?](#why-virtualisations-)
    + [Container vs Virtual Machine](#container-vs-virtual-machine)
      - [Key Benefits](#key-benefits)
    + [Why image](#why-image)
  * [Docker architecture](#docker-architecture)
  * [Docker Hands on](#docker-hands-on)
    + [Docker CLI](#docker-cli)
    + [docker image](#docker-image)
      - [console-helloworld](#console-helloworld)
      - [Docker file 例子：](#docker-file----)
      - [web-nginx](#web-nginx)
      - [3.web-python-flask](#3web-python-flask)
    + [console-dependency](#console-dependency)
      - [Cleanup](#cleanup)
    + [Docker-compose](#docker-compose)
    + [docker-registry](#docker-registry)
    + [Share](#share)
    + [Container threat model](#container-threat-model)
    + [container image](#container-image)
  * [3.资料阅读](#3----)
    + [More to read](#more-to-read)
    + [Best Practices](#best-practices)
  * [4.Homework](#4homework)


# 老师介绍
Liji Yu 老师-- 浙江大学计算机专业硕士。 十多年互联网从业经验。
Senior Infrastructure Engineer - Canva

# 1.Contents
- Docker basics
- Docker CLI
- Docker images & Dockerfile
- Docker registry
- Docker compose
- Docer security

# 2.Docker basics
## Devops cycle and our course
- Coding and test
	- How do I configure the environment?
	- Configure step by step
	- Can we do better?
- Deploy the code to staging
	- Works for me problem
- Deploy the code to production
	- 预发布和生产环境有出入

- Bundle codes and all configs
- Build once
- Run everywhere
本地，预发布，生产的环境通过打包的方式，保持环境一致，但是实际工作情况只能尽可能的保持一致。

## Docker introduction

Container 系统-----程序员只需要专心写程序就好，不管底层的系统。更好的利用资源，轻量级。

### What is Docker?
* Docker is a set of platform as a service (PaaS) products that use OS-level virtualization to deliver software in packages called containers.
* Containers are isolated from one another and bundle their own software, libraries and configuration files;
	
	提供操作系统层面的虚拟化，每个容器都是单独隔离的  
	不是所有的容器都是docker，docker是容器且是业界标准
	将一台物理机器，虚拟成多个虚拟的机器，容器根据镜像启动的虚拟机器里面已经有很多
		Physical computer ->virtual computer = virtualisations
		Pre-configured image & Resource efficient

* Large web deployments like Google and Twitter, and platform providers such as Heroku run on container technology,  at a scale of hundreds of thousands or even millions of containers.
### Why Virtualisations?
Resource utilization
	一台电脑上跑很多的service
	Isolation
		多个Service同时使用需要隔离环境，否则会产生资源冲突（端口监听冲突，CPU，memory）
	资源限制

Docker enables developers to easily pack, ship, and run any application as a lightweight, portable, self-sufficient container, which is easily distributable and can run virtually anywhere.

Docker has been designed in a way that it can be incorporated into most DevOps applications, including Puppet, Chef, Vagrant, Ansible and many more, or it can be used on its own to manage development environments.
### Container vs Virtual Machine
两者都是把一台物理机器隔离成多台虚拟的机器
区别是实现的方式和实现的层级，虚拟机在更低的层级（虚拟物理硬件），在虚拟硬件上装操作系统，虚拟需要装OS，运行OS要浪费资源；container在操作系统上隔离出的不同的环境，坏处容器上是不能装其他的操作系统，Docker一般只支持Linux系统，好处是相当快，秒起Linux环境。多跑好几个并且占用的资源更少。

Container是集装箱概念，标准化， 一般会在Container中放没有状态的应用。1. 概念上，理想情况我们可以把所有应用程序放入Container，实际上存储服务器不会用容器。

#### Key Benefits

* Portable deployment across machines.
  * If you sent me a copy of your application installed in a custom LXC configuration, the app will be tied to your 
    machine’s specific configuration: networking, storage, logging, etc. 
  * Instead, Docker defines an abstraction for these machine-specific settings.

* Application-centric. 
  * Docker is optimized for the deployment of applications, as opposed to machines. This is reflected in its API, 
    user interface, design philosophy and documentation.

* Automatic build. 
  * Docker includes a tool for developers to automatically assemble a container from their source code, with full 
    control over application dependencies, build tools, packaging etc. They are free to use make, maven, chef, puppet, 
    salt, Debian packages, RPMs, source tarballs, or any combination of the above, regardless of the configuration of
    the machines.

* Versioning. 
  * Docker includes git-like capabilities for tracking successive versions of a container, inspecting the 
  diff between versions, committing new versions, rolling back etc.

* Component re-use
  * Any container can be used as a parent image to create more specialized components.
    
* Sharing.
  * Docker has access to a public registry on Docker Hub where thousands of people have uploaded useful images: 
    anything from Redis, CouchDB, PostgreSQL to IRC bouncers to Rails app servers to Hadoop to base images for various
    Linux distros. The registry also includes an official “standard library” of useful containers maintained by the 
    Docker team. The registry itself is open-source, so anyone can deploy their own registry to store and transfer 
    private containers, for internal server deployments for example.
* Tool Ecosystem
  * Great Integration Capabilities with pretty much everything that you can think of.
### Why image
跑容器所需要的所有文件和配置信息打成的一个包。build一个image，可用于多次启动.
**Build image once, launch 100 times.**

## Docker architecture
- docker daemon
- client
	- CLI
	- GUI
- Container
- Image
- Resources
	- network
	- data volume

## Docker Hands on
### Docker CLI
It is a command line interface client for interacting with the Docker daemon. It greatly simplifies how you manage container instances and is one of the key reasons why developers love using Docker.

- Run a docker container from nginx demo image
	```
	docker run -d -p 80:80 --name demo nginxdemos/hello
	```
	-d 当前容器是否运行在后台
	-p 端口映射
	--name 容器命名（不起名字可以用容器id操作）
  
- List docker containers
  	```
	docker ps
  	```
   
- List docker images
 	```
	docker images
 	```
  
- View logs in the container
 	```
	docker logs -f demo
 	```
  	-f follow当前容器的log记录
  
- Inspect the container
 	```
	docker inspect demo
 	```
也可以用UI查看容器的inspect，即容器各种细节参数

- Copy a file to the container
 	```
	docker cp your_file demo:/
 	```
容器内的文件与原系统中的文件是隔离的，该命令用于拷贝原系统中的文件到容器内。

- Enter the container and execute command inside the container
 	```
	docker exec -it demo sh
	
	ls
	ps -ef
	whoami
	uname -a
	cd /etc/nginx/
	ls
	cat nginx.conf
	exit
 	```
	该命令用于登录到已经启用的容器内
	-sh 可以是任何容器内可以执行的命令
	-it 容器的输入输出和电脑的输入输出连起来，需要terminal时要加上-it，share输入输出
	exit or control+D 退出

- Stop the container (or kill the container)
 	```
	docker stop demo
	or

	docker kill demo
 	```
	image是模版

- View hidden container which you cannot view via docker ps
 	```
	docker ps -a
 	```
  	stop的容器其实还在，而且它占用着容器名，ps查看在运行的容器， ps -a 查看所有的容器
  
- Remove the container
	```
 	docker rm demo
 	```
 也可以将容器跑在前台（不加-d）
 	```
  	docker run -it --rm -p 80:80 --name demo nginxdemos/hello
   	```
    	-d 没有-d意味着当前terminal窗口用于监视该容器，（该terminal不可以干其他的事情）
	-it share容器的输入输出
 	--rm 容器停掉的时候，该容器将被自动删除，直接释放容器名字
  
- Run the same container on a different port
	```
 	docker run -p 8080:80 --name demo8080 nginxdemos/hello
 	```
	notice this time we run it without -d, what is the difference?

	Try visit http://127.0.0.1 and http://127.0.0.1:8080

	about published ports: https://docs.docker.com/network/#published-ports
	-p 是容器的端口映射设置，-p 8080:80 意味着本机上的8080端口映射到容器的80端口上
- Let's change some file
 	```
	docker exec -it demo8080 sh
	
	echo '<h1>hello jiangren</h1>' > /usr/share/nginx/html/index.html
  	```
	更改了容器内index.html文件的内容
  	更改的内容将跟随该容器存在，如果容器删除，更改的内容也将删除，如果只是停止后再启动该容器，更改的内容还将存在。
  	如果想让保存更改的内容需要添加volume
- Container with a volume
  container内部的文件是一个临时的文件存储系统，如果想持久存储内容，需要创建一个volume,然后挂载在容器上
  ```
  docker volume create my-vol
  docker volume ls
  docker volume inspect my-vol
  docker run -it -p 8080:80 -v my-vol:usr/share/nginx/html -- demo8080 nginxdemos/hello
  ```
  空卷（Volume）第一次挂载时：当一个新的（空的）卷第一次挂载到容器时，如果容器内挂载点目录中已经存在文件，这些文件会被复制到卷中。因此，卷最初会包含容器内挂载点目录中的所有文件和子目录。
  非空卷挂载时：当一个已经包含数据的卷挂载到容器时，卷中的数据将会覆盖容器内挂载点目录中的任何现有数据。

### docker image
创建一个自己的image
用dockerfile写自己的image

#### console-helloworld

详细参考链接:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK3_Dockerisation/dockerfile/1.console-helloworld>

简单快速的方法，可以说是一键直接运行，看以下代码（会按照dockerfile里面的代码按照顺序执行）：
```
./run.sh
```

-实际的分步操作步骤：
1. 先Build这个image
Build a image named `jr/console-helloworld`.
```
cd 1.console-helloworld

docker build -t jr/console-helloworld .

docker images
```

2.再run这个image
```
docker run --rm jr/console-helloworld
```
Note: Use `--rm` option to remove container automatically.

以下是Dockerfile文件里的代码：
```
FROM ubuntu

ENTRYPOINT ["/bin/bash"]
CMD ["-c", "echo Hello world from command line."] 
```
所有的image都需要一个基础的镜像，此镜像的基础镜像 from ubuntu
不装任何东西，不改任何内容，只告诉此docker启动的时候做什么事情。
ENTRYPOINT 和 CMD 的命令都需要写成【】（list），
实际的效果是将这两个命令连起来
命令运行完就结束了
分成两部分是因为CMD部分可以在运行的时候重载。

以下是run.sh文件里的代码：
```
#!/bin/bash
set -e

cd "$(dirname "$0")"

echo "Building image ..."
docker build -t jr/console-helloworld .

echo
echo "Running container ..."
docker run --rm jr/console-helloworld
```

#### Docker file 例子：
```
FROM python:3

# set a directory for the app
WORKDIR /usr/src/app

# copy all the files to the container
COPY . .

# install dependencies
RUN pip install --no-cache-dir -r ./src/requirements.txt

# define the port number the container should expose
EXPOSE 5000

# run the command
CMD ["python", "./src/app.py"]
```

几个注意点：
   Flask (Python framework)
   http: default port 80
   https: default port 443
   you can use ports after 1024 like port 5000 or  port 5001

#### web-nginx

详细参考链接:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK3_Dockerisation/dockerfile/2.web-nginx>

同以上实验#1相同2种方法： 1. 直接一键运行 ./run.sh  2. 先build再run
这个实验需要注意的点：

在build的步骤里面：
-t代表是 tag的意思，一个规范的模式是这样写：
	jr/web-nginx  第一部分jr是你的用户名，第二部分web-nginx才是你的image真正的名字，可以在后面加:1.0 或 2.0等版本号
版本号可根据需要添加，如果不添加默认是latest

Build an image named `jr/web-nginx`.
```
cd 2.web-nginx

docker build -t jr/web-helloworld .

docker images
```

当我们在浏览器里面hit htmL文件时候，不用写端口号，用默认的80端口----> nginx 可以去查，它的默认端口是 80端口（可以去docker hub查看 nginx的文件，EXPOSE 80  代表预设值是 80端口）

Dockerfile
```
FROM nginx

COPY ./index.html /usr/share/nginx/html
```
将自己写的index.html文件放入到nginx中，像一个实际开发的流程，开发的产品index.html放打成镜像，之后用该镜像安装的容器都带有该index.html文件

#### 3.web-python-flask

详细参考链接:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK3_Dockerisation/dockerfile/3.web-python-flask>

同以上实验#1相同2种方法： 1. 直接一键运行 ./run.sh  2. 先build再run
这个实验需要注意的点：
-类似我们之前的CLI实验
-从以下docker file 里面可以看出，这个image自带包含了 nginx,flask就不需要安装了，唯一要安装的是PYTHON的 requests这个library(第二行RUN pip install requests),然后有了nginx了也不用设置端口了，用nginx的端口，所以直接把app文件COPY过去就行
```
FROM tiangolo/uwsgi-nginx-flask:python3.7

RUN pip install requests

COPY ./app /app
```
```
cd 3.web-puthon-flask

docker build -t jr/web-flask .
docker images
```
用‘.‘表示当前路径

-运行完之后
In the browser, open http://localhost/?name=Tom and you will get a “Hello + <name> 
以上这个 ?city=jin 的部分 叫  query string，可以用于查询

此镜像拷贝了整个目录，起这个flask，代码拷贝，还要确保requests库存在，因此要‘run pip install requests',Run指令是最常见的。

第三个实验得出的小总结：比起之前2个实验，越来越方便，越来越简单，因为把该用的软件都整合到一个 image里面，可见，run一个小的网站无非3步：
 1.找到好用的image (可以去docker hub找)
 2.把它的 dependancy安装好
 3.程序运行起来

### console-dependency

详细参考链接:<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK3_Dockerisation/dockerfile/4.console-dependency>

在实验3的基础上，自动帮我们搜索, au,sh,等citymatcher关键字,不需要我们手动一个个去浏览器里面敲:
<http://localhost/?city=au>
<http://localhost/?city=sh>
<http://localhost/?city=ing>

然后自动返回结果，在command line里面提供这些信息

--->通过分析Dockerfile，我们来看看是怎么实现的呢？
在ubuntu里面装了curl的功能，然后运行 main.py
```
FROM ubuntu

RUN apt-get update && apt-get install -y curl && apt-get autoclean

COPY app/main.sh .

ENTRYPOINT ["/bin/bash"]

CMD ["main.sh"] 
```

Build
```
cd 4.console-dependency

docker build -t jr/console-hello .
docker images
```

--->通过分析main.py -如下，运行了一个bash script(第6第7周会学到),常用！ 在linux系统里面想自动化一些东西会用到
echo---> 就是打印的意思类似 print
循环2次：
for循环嵌套  里面的for key 有 （sy au ing ba lu pa ji san）

```
#!/bin/bash

echo "Press Ctrl+C any time to stop the application."
echo

for i in $(seq 2); do
    for key in sy au ing ba lu pa ji san ; do
        echo "Searching all cities that contains '${key}' keyword ..."
        curl http://citymatcher?city=${key} 2>/dev/null
        echo
        echo
        
        sleep 3

    done
done
```
Run
```
docker run --link webflask --rm jr/console-hello
```
Use --link option to link a another container.
Use --rm option to remove container automatically.

2个container 进行通信， link 查文档:
链接：<https://docs.docker.com/network/links/>
 Legacy container links
 The --link flag is a legacy feature of Docker. It may eventually be removed. Unless you absolutely need to continue using it, we recommend that you use user-defined networks to facilitate communication between two containers instead of using --link. One feature that user-defined networks do not support that you can do with --link is sharing environment variables between containers. However, you can use other mechanisms such as volumes to share environment variables between containers in a more controlled way.

 
#### Cleanup
Just simply kill all the containers simply.
```
docker kill $(docker ps -q)
```

总结 docker file 的命令，Common Dockerfile Commands：
FROM
ENV
WORKDIR
COPY
RUN
EXPOSE
ENTRYPOINT
以上命令和详细信息，还有一些小练习可以参考链接:<https://github.com/JiangRenDevOps/DevOpsNotes/blob/master/WK3_Dockerisation/0.docker-intro.md>

### Docker-compose 
-学习dockerfile 之后，可以使用docker-compose同时启动多个container
参考链接：<https://github.com/JiangRenDevOps/DevOpsNotes/tree/master/WK3_Dockerisation/docker-compose>


要学会看以下的yml文件:
```
version: '3'
services:
  console-helloworld:
    image: jr/console-helloworld
    build: ../dockerfile/1.console-helloworld
  web-nginx:
    image: jr/web-helloworld
    build: ../dockerfile/2.web-nginx
    ports:
    - "8080:80"
  web-python-flask:
    image: jr/web-citymatcher
    build: ../dockerfile/3.web-python-flask
    ports:
    - "80:80"
  console-dependency:
    image: jr/console-citymatcher
    build: ../dockerfile/4.console-dependency
    links:
    - web-python-flask:citymatcher
    depends_on:
    - web-python-flask
```

主要命令：

```
#Start compose
docker-compose up
```

通过这个docker-compose命令和配置，把我们刚刚练习的4个CONTAINER都运行起来了，自己运行，运行完了就自己清理关闭掉。在production环境里面，我们通常会有一个主程序，在主程序周围也会有一些副程序来运行一些功能来辅助主程序。可以通过docker-compose来启动。---》 后续我们会学到 K8S,或者 DOCKER SWARM来做 ORCHESTRATION

docker-compose.yml file（yml有自己的格式，自己掌握）

Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to
configure your application’s services. Then, with a single command, you create and start all the services from your configuration.

If you finished today's exercises try: https://docs.docker.com/compose/gettingstarted/

### docker-registry
dockerhub的帐号上可以push自己的image

链接:<https://github.com/JiangRenDevOps/DevOpsNotes/blob/master/WK3_Dockerisation/4.docker-registry.md>


### Share
Pull an image from a registry

docker pull myimage:1.0
Retag a local image with a new image name and tag

docker tag myimage:1.0 myrepo/myimage:2.0
Push an image to a registry

docker push myrepo/myimage:2.0


### Container threat model
- Insecure Host (host上有漏洞）
- Use an image with a vulnerability （image上做手脚）
- Expose secrets in image
- Container escape vulnerabilities（通过黑容器进而黑host）
- ...(cheatsheet)

### container image 
- docker official image
- 用工具扫描（docker scout）,扫描已知的漏洞并提供修改建议
  ```
  docker scout quickview jr/web-flask:latest
  ```
  修改推荐的basic image 或者 自己建basic image
- 尽量不要用第三方的image



## 3.资料阅读

### More to read

https://docs.docker.com/engine/reference/builder/

https://docs.docker.com/compose/compose-file/compose-file-v3/

https://docs.docker.com/compose/faq/

### Best Practices 
Please read these pages to understand the best practices 

https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

https://takacsmark.com/dockerfile-tutorial-by-example-dockerfile-best-practices-2018/#overview

https://docs.docker.com/ci-cd/github-actions/


## 4.Homework
Find a trending dockerised service from github that interests you the most e.g. https://github.com/search?o=desc&q=dockerised&s=stars&type=Repositories

Task1: Try to read and understand the docker-cli, dockerfile and docker-compose file.
Task2: Explain line-by-line to your team member.
Task3: Try spin up the service follow the README file.
