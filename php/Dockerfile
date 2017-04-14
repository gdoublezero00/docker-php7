FROM php:7-apache
COPY php-general.ini /usr/local/etc/php/

ENV TZ=Asia/Tokyo

#
#Package Install
#
RUN apt-get update && apt-get install -y apt-transport-https
RUN echo deb http://packages.dotdeb.org jessie all >> /etc/apt/sources.list 
RUN curl https://www.dotdeb.org/dotdeb.gpg | apt-key add - \
	&& curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
	&& curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
RUN apt-get update && apt-get install -y php7.0-sybase freetds-common freetds-bin freetds-dev unixodbc 

RUN apt-get install -y zip git libaio1 libmcrypt-dev freetds-dev libicu-dev libpq-dev libsqlite3-dev libssl-dev libpng-dev  zlib1g-dev unixodbc-dev gcc g++ build-essential \
	&& cp -s /usr/lib/x86_64-linux-gnu/libsybdb.so /usr/lib/

#COPY Oracle InstantClient
ADD instantclient_12_1.tar.bz2 /usr/local/
ENV ORACLE_HOME=/usr/local/instantclient_12_1 \
NLS_LANG=Japanese_Japan.AL32UTF8
RUN ln -s /usr/local/instantclient_12_1/libclntsh.so.12.1 /usr/local/instantclient_12_1/libclntsh.so
RUN docker-php-ext-configure oci8 --with-oci8=instantclient,/usr/local/instantclient_12_1,12.1 \ 
	&& docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/usr/local/instantclient_12_1,12.1

RUN docker-php-ext-configure oci8 --with-oci8=instantclient,/usr/local/instantclient_12_1,12.1 
RUN docker-php-ext-install mbstring fileinfo mcrypt mysqli pdo_mysql pdo_dblib pdo_sqlite gd oci8 pdo_oci

RUN a2enmod rewrite

RUN pecl install mongodb redis sqlsrv pdo_sqlsrv pdo_dblib \
	&& echo "extension=mongodb.so" > /usr/local/etc/php/conf.d/docker-php-ext-mongodb.ini \
	&& echo "extension=redis.so" > /usr/local/etc/php/conf.d/docker-php-ext-redis.ini

RUN curl -L -o /tmp/composer-setup.php https://getcomposer.org/installer 
RUN php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer

RUN service apache2 restart
