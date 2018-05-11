---
layout: post
title:  "安装Laradock与Xdebug在PHPstorm中调试配置"
date:   2018-05-08 11:00:00 +0800
categories: Docker
---

## 安装Laradock

### 安装docker

在Ubuntu1804下，直接使用apt安装docker即可

###安装docker-compose

docker-compose下载地址：https://github.com/docker/compose/releases/tag/1.17.0

下载docker-compose，复制到/usr/local/bin/docker-compose（或建立软连接）

```shell
sudo chmod +x docker-compose

sudo gpasswd -a ${USER} docker
```

### 安装laradock

laradock官网：http://laradock.io/

克隆laradock

`git clone https://github.com/Laradock/laradock.git`

拷贝配置文件

`cp env-example .env`

构建运行容器

`docker-compose up -d nginx mysql phpmyadmin redis workspace `

构建可能由于网络原因无法顺利执行。可以将php的redis扩展pecl安装修改成编译安装，示例：

在pecl网站上下载redis-3.1.4.tgz并复制到php-fpm目录下，然后修改dockerfile：

```shell
# COPY压缩包，可以放在顶部，这样其他扩展编译安装不需要重复执行了
COPY ./*.tgz /tmp/

###########################################################################
# PHP REDIS EXTENSION
###########################################################################

ARG INSTALL_PHPREDIS=false

RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
    # Install Php Redis Extension
    # printf "\n" | pecl install -o -f redis \
    # &&  rm -rf /tmp/pear \
    # &&  docker-php-ext-enable redis \
    cd /tmp/ \
    && tar -xf redis-3.1.4.tgz \
    && rm redis-3.1.4.tgz \
    && ( cd redis-3.1.4 && phpize && ./configure && make && make install ) \
    && rm -r redis-3.1.4 \
    && docker-php-ext-enable redis \
;fi
```
并注释掉workspace文件夹下的dockerfile的redis相关代码
```shell
###########################################################################
# PHP REDIS EXTENSION
###########################################################################

ARG INSTALL_PHPREDIS=false
ARG PHP_VERSION=${PHP_VERSION}

#RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
#    # Install Php Redis extension
#    printf "\n" | pecl -q install -o -f redis && \
#    echo "extension=redis.so" >> /etc/php/${PHP_VERSION}/mods-available/redis.ini && \
#    phpenmod redis \
#;fi
```

其他扩展如xdebug也同样

```shell
###########################################################################
# xDebug:
###########################################################################

ARG INSTALL_XDEBUG=false

RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    # Install the xdebug extension
    cd /tmp/ \
            && tar -xf xdebug-2.7.0alpha1.tgz \
    		&& rm xdebug-2.7.0alpha1.tgz \
            && ( cd xdebug-2.7.0alpha1 && phpize && ./configure && make && make install ) \
            && rm -r xdebug-2.7.0alpha1 \
            && docker-php-ext-enable xdebug \
    # pecl install xdebug && \
    # docker-php-ext-enable xdebug \
;fi

# Copy xdebug configuration for remote debugging
COPY ./xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
```



---

## 开启Xdebug

https://pecl.php.net/package/xdebug

下载xdebug的压缩包，与开启redis扩展相同，放在php-fpm的目录下，然后修改dockerfile对应的代码（参照上文）

重新build之后，可以打印phpinfo查看是否已经开启xdebug扩展。

![]({{ site.resourceurl }}2018-05-11-16:24:26.png)

## phpstorm开启调试

![]({{ site.resourceurl }}2018-05-08-15:51:31.png)

PHPstorm下使用xdebug调试php代码

可以在chrome浏览器安装插件：https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc

- xdebug-helper

PHPstorm的servers配置：laradock容器

![]({{ site.resourceurl }}2018-05-08-15:52:15.png)

![]({{ site.resourceurl }}2018-05-08-15:52:45.png)

![]({{ site.resourceurl }}2018-05-08-15:53:21.png)

![]({{ site.resourceurl }}2018-05-08-15:54:36.png)

![]({{ site.resourceurl }}2018-05-08-15:55:44.png)

![]({{ site.resourceurl }}2018-05-08-16:00:36.png)

除了按照上图对PHPstorm进行配置之外，还要配置xdebug相关的参数

php-fpm与workspace目录下的xdebug.ini，修改
```shell
xdebug.remote_autostart=1
xdebug.remote_enable=1
```
