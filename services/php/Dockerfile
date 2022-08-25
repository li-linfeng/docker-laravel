FROM php:7.4-fpm-alpine3.13

# 更新安装源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories

# 设置时区为上海
RUN apk update && apk add --no-cache tzdata autoconf gcc g++ make zlib-dev  curl-dev\
    && apk add --no-cache --update rabbitmq-c-dev \
    && cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone \
    && apk del tzdata 


RUN apk add --no-cache \
    freetype \
    libjpeg-turbo \
    libpng \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    && docker-php-ext-configure gd \
    --with-freetype=/usr/include/ \
    --with-jpeg=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-enable gd \
    && apk del --no-cache \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    && rm -rf /tmp/*

RUN apk add libzip-dev

RUN docker-php-ext-install pdo pdo_mysql zip bcmath




# package build
RUN mkdir /packages

# redis extension
COPY ./redis-4.1.0.tgz  /packages/redis-4.1.0.tgz 

RUN   cd /packages \
    && tar -xzvf redis-4.1.0.tgz \
    && cd redis-4.1.0 \
    && /usr/local/bin/phpize \
    && ./configure --with-php-config=/usr/local/bin/php-config \
    && make && make install \
    && cd ../ \
    && docker-php-ext-enable redis

# wkhtmltopdf
COPY ./wkhtmltopdf /bin/
RUN chmod +x /bin/wkhtmltopdf

# 安装字体
RUN mkdir -p /usr/share/fonts/chinese/TrueType/
COPY   ./simsun.ttc /usr/share/fonts/chinese/TrueType/
COPY   ./simsun.ttf /usr/share/fonts/chinese/TrueType/

COPY ./upload.ini /usr/local/etc/php/conf.d

# add composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/bin/composer


#运行服务

WORKDIR /www/aggreagtion-api
