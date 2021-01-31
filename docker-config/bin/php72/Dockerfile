FROM alpine:3.9

# expose ports
EXPOSE 80/tcp
EXPOSE 443/tcp

# update apk repositories
RUN apk update

# upgrade all
RUN apk upgrade

# install console tools
RUN apk add inotify-tools

# install zsh
RUN apk add zsh zsh-vcs

# install php
RUN apk add \
    php7-fpm \
    php7-bcmath \
    php7-common \
    php7-ctype \
    php7-curl \
    php7-dom \
    php7-fileinfo \
    php7-gettext \
    php7-json \
    php7-mbstring \
    php7-mysqli \
    php7-opcache \
    php7-openssl \
    php7-pdo \
    php7-pdo_mysql \
    php7-pdo_sqlite \
    php7-posix \
    php7-session \
    php7-simplexml \
    php7-sodium \
    php7-tokenizer \
    php7-xml \
    php7-xmlwriter \
    php7-zip \
    php7-intl \
    php7-gd \
    php7-exif

# install composer
RUN apk add composer

# install apache
RUN apk add apache2 apache2-proxy
    # apache2-ssl \

# enable important apache modules
RUN sed -i 's|#LoadModule rewrite_module modules/mod_rewrite.so|LoadModule rewrite_module modules/mod_rewrite.so|g' /etc/apache2/httpd.conf
RUN sed -i 's|LoadModule mpm_prefork_module modules/mod_mpm_prefork.so|#LoadModule mpm_prefork_module modules/mod_mpm_prefork.so|g' /etc/apache2/httpd.conf
RUN sed -i 's|#LoadModule mpm_event_module modules/mod_mpm_event.so|LoadModule mpm_event_module modules/mod_mpm_event.so|g' /etc/apache2/httpd.conf
RUN sed -i 's|#LoadModule deflate_module modules/mod_deflate.so|LoadModule deflate_module modules/mod_deflate.so|g' /etc/apache2/httpd.conf
RUN sed -i 's|#LoadModule expires_module modules/mod_expires.so|LoadModule expires_module modules/mod_expires.so|g' /etc/apache2/httpd.conf
RUN sed -i 's|#LoadModule ext_filter_module modules/mod_ext_filter.so|LoadModule ext_filter_module modules/mod_ext_filter.so|g' /etc/apache2/httpd.conf

# authorize all directives in .htaccess
RUN sed -i 's|    AllowOverride None|    AllowOverride All|g' /etc/apache2/httpd.conf

# change log files location
RUN mkdir -p /var/log/apache2
RUN sed -i 's| logs/error.log| /var/log/apache2/error.log|g' /etc/apache2/httpd.conf
RUN sed -i 's| logs/access.log| /var/log/apache2/access.log|g' /etc/apache2/httpd.conf

# authorize all changes in htaccess
RUN sed -i 's|Options Indexes FollowSymLinks|Options All|g' /etc/apache2/httpd.conf

# configure php-fpm to use unix socket
RUN sed -i 's|listen = 127.0.0.1:9000|listen = /var/run/php-fpm7.sock|g' /etc/php7/php-fpm.d/www.conf
RUN sed -i 's|;listen.owner = nobody|listen.owner = apache|g' /etc/php7/php-fpm.d/www.conf

# use set handler to route php requests to php-fpm
RUN sed -i 's|^DocumentRoot|<FilesMatch "\.php$">\n\
    SetHandler "proxy:unix:/var/run/php-fpm7.sock\|fcgi://localhost"\n\
</FilesMatch>\n\nDocumentRoot|g' /etc/apache2/httpd.conf

# update directory index to add php files
RUN sed -i 's|DirectoryIndex index.html|DirectoryIndex index.php index.html|g' /etc/apache2/httpd.conf

# add entry point script
ADD --chown=root:root includes/start.sh includes/vhosts.conf /tmp/

# make entry point script executable
RUN mv /tmp/vhosts.conf /etc/apache2/conf.d/vhosts.conf
RUN chmod +x /tmp/start.sh


# set working dir
WORKDIR /var/www/html/

# set entrypoint
ENTRYPOINT ["/tmp/start.sh"]
