# 如何使用

## 1、将 Dockerfile 内容拷贝到本地

```sh
$ mkdir ~/docker-images/jdk8
$ cd ~/docker-images/jdk8
$ vim Dockerfile
```

完成后，按 `:wq` 保存退出。



## 2、构建镜像

```sh
$ docker build -t gukt/openjdk:8-jdk-centos7
```

等待构建 `image` 成功。



## 3、查看构建成功的镜像

```sh
$ docker images
```



## 4、运行容器

```sh
$ docker run \
	-it \
	-d \
	--name jdk8 \
	gukt/openjdk:8-jdk-centos7 bash
```

注意：如果不指定在容器内运行 `bash`，容器运行打印出 `java` 版本号后就会退出。



## 5、以交互方式进入容器

```sh
$ docker exec -it jdk8 bash
```

