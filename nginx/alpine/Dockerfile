FROM alpine:3.10

LABEL version="1.0.0"
LABEL maintainer="gukt <29283212@qq.com>"
LABEL discription="这是基于alpine构建的nginx镜像，添加了echo模块以及ssl等常用模块"

#设置容器的工作目录
WORKDIR ~
ENV NGINX_VERSION=1.16.1

RUN set -x \
    && addgroup -g 101 -S nginx \
    && adduser -S -D -H -u 101 -h /var/cache/nginx -s /sbin/nologin -G nginx -g nginx nginx \
    #使用阿里仓库加速apk安装
    && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    #首先安装必要的依赖或工具
    && apk update && apk --no-cache add \
        vim \
        gcc \
        libc-dev \
        make \
        pcre-dev \
        zlib-dev \
        openssl-dev \
        gd-dev \
        geoip-dev \
        perl-dev \
        libedit-dev \
        bash \
        tzdata \
        curl

RUN set -x \
    && cd /tmp \
    #下载最新版nginx源代码并解压
    #其他版本请见: http://nginx.org/download
    && wget http://nginx.org/download/nginx-$NGINX_VERSION.tar.gz \
    && tar -xzf nginx-$NGINX_VERSION.tar.gz \
    \
    #还希望将echo模块编译进去，下载最新版的echo模块源代码并解压：
    #其他版本请见: https://github.com/openresty/echo-nginx-module/releases
    && wget https://github.com/openresty/echo-nginx-module/archive/v0.61.tar.gz \
    && tar -zxvf v0.61.tar.gz \
    \
    #nginx源码中提供了让vim支持识别nginx语法的相关文件，拷贝到~/.vim
    && mkdir -p ~/.vim \ 
    && cp -r /tmp/nginx-$NGINX_VERSION/contrib/vim/* ~/.vim/

#进入nginx源码目录，开始编译，添加echo-nginx-module-0.61模块，其他模块自选
#NOTE: 以下很多path参数设定的都是文件名
RUN set -x \
    && mkdir -p /etc/nginx/conf.d \
    && mkdir -p /var/log/nginx \
    && cd /tmp/nginx-$NGINX_VERSION \
    && ./configure \
        --prefix=/usr/share/nginx/ \
        --sbin-path=/usr/sbin/nginx \
        --conf-path=/etc/nginx/nginx.conf \
        --pid-path=/var/run/nginx.pid \
        --lock-path=/var/run/nginx.lock \
        --http-log-path=/var/log/nginx/access.log \
        --error-log-path=/var/log/nginx/error.log \
        --with-http_stub_status_module \
        --with-http_ssl_module \
        --with-http_realip_module \
        --with-http_sub_module \
        --with-http_gzip_static_module  \
        --add-module=/tmp/echo-nginx-module-0.61 \
    && make && make install 
    
#以下对vim进行一些配置，方便容器内使用vim编辑nginx配置文件
#让VIM支持nginx语法
RUN  set -x \
    #配置vim中tab的一些选项
    && echo '' >> /etc/vim/vimrc \
    && echo 'set ts=4' >> /etc/vim/vimrc \
    && echo 'set expandtab' >> /etc/vim/vimrc \
    && echo 'set autoindent' >> /etc/vim/vimrc \
    #删除本次安装编译过程中留下的所有临时文件
    && rm -rf /tmp/* \
    # forward request and error logs to docker log collector
    && ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

EXPOSE 80

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]
