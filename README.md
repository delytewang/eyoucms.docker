# eyoucms.docker
dockerfile for eyoucms

## 目的
最近帮一个客户迁移主页，看到了[eyoucms](https://www.eyoucms.com/)，基本符合客户要求。主页需要部署在客户服务器上，考虑到客户服务器上已经有不少客户的服务在运行，为了减少影响及风险，选择docker来进行环境隔离，避免主页服务与原服务产生冲突。

## 使用说明
### 软件依赖
根据[eyoucms安装说明](https://www.eyoucms.com/help/azwt/2020/1012/11028.html)，需配置下列软件：

* Apache 服务器
* PHP版本：5.4及5.4以上，推荐使用5.6
* MYSQL 5.0以上，推荐使用5.7+

### dockerfile 配置
安装时需要 GD库、mysqli、pdo及mysql_pdo 在内的php扩展。

```dockerfile
FROM php:7.4-apache
RUN docker-php-ext-install mysqli pdo pdo_mysql
RUN apt-get update && apt-get install -y \
                libfreetype6-dev \
                libjpeg62-turbo-dev \
                libpng-dev \
        && docker-php-ext-configure gd --with-freetype --with-jpeg \
        && docker-php-ext-install gd
```

### docker-compose 配置
```docker-compose
version: '2'
services:
    db:
        image: mysql:5.7
        ports:
            - 23306:3306
        volumes:
            - "./.data/db:/var/lib/mysql"
        environment:
            MYSQL_ROOT_PASSWORD: set_root_password
            MYSQL_DATABASE: eyou
            MYSQL_USER: eyou
            MYSQL_PASSWORD: set_user_password
    php:
        image: delyte/php
        ports:
            - 7080:80
        volumes:
            - ".:/var/www/html"
        links:
            - db:db
```

