
FROM php:8.2-fpm
LABEL MAINTAINER juanda <juandacorreo@gmail.com>

ARG VERSION=4.1.1
# ARG MOOSH_VERSION=0.36 varible not used, always latest moosh version.
ARG COMPOSER_VERSION=1.10.6
ENV DOCKERIZE_VERSION v0.6.1

VOLUME ["/var/moodledata"]
EXPOSE 80

# Let the container know that there is no tty JUST WHEN INSTALLING!
# Later we can run docker -ti and we need interactivity

ARG DEBIAN_FRONTEND=noninteractive

## extensions based on https://github.com/moodlehq/moodle-php-apache/blob/master/Dockerfile
# COPY libicu63_63.1-6+deb10u1_amd64.deb /tmp
COPY php-extensions.sh /tmp
# TODO: Valorar usar esto https://github.com/mlocati/docker-php-extension-installer para la instalación de extensiones
RUN /tmp/php-extensions.sh

# set recommended opcache settings 
# see https://www.php.net/manual/en/opcache.installation.php
## see https://docs.moodle.org/39/en/OPcache
RUN { \
	echo 'opcache.memory_consumption=1024'; \
	echo 'opcache.interned_strings_buffer=8'; \
	echo 'opcache.max_accelerated_files=10000'; \
	echo 'opcache.revalidate_freq=60'; \
	echo 'opcache.fast_shutdown=1'; \
	echo 'opcache.enable_cli=1'; \
	echo 'opcache.use_cwd=1'; \
	echo 'opcache.validate_timestamps = 1'; \
	echo 'opcache.save_comments=1'; \
	echo 'opcache.enable_file_override=0'; \
	} > /usr/local/etc/php/conf.d/opcache.ini

# usefull for moodle

RUN { \
	echo 'file_uploads = On'; \
	echo 'memory_limit = 512M'; \
	echo 'upload_max_filesize = 64M'; \
	echo 'post_max_size = 64M'; \
	echo 'max_execution_time = 600'; \
	} > /usr/local/etc/php/conf.d/uploads.ini


RUN	echo "Installing moodle"; \
	curl -o moodle.tgz -fSL "https://download.moodle.org/download.php/direct/stable401/moodle-${VERSION}.tgz"; \
	mkdir /usr/src/moodle; \
	tar -xf moodle.tgz -C /usr/src/moodle --strip 1; \
	rm moodle.tgz

RUN	echo "Installing composer"; \
	curl -o composer.phar -fSL "https://github.com/composer/composer/releases/download/${COMPOSER_VERSION}/composer.phar"; \
	chmod +x ./composer.phar; \
	mv ./composer.phar /usr/local/bin/composer

RUN	echo "Installing moosh"; \ 
	cd /usr/src; \
	git clone https://github.com/tmuras/moosh.git; \ 
	cd moosh; \
	composer install; \
	ln -s /usr/src/moosh/moosh.php /usr/local/bin/moosh

# RUN echo "Installing moosh"; \
# 	cd /usr/src; \
# 	curl -o moosh.zip -fSL "https://moodle.org/plugins/download.php/28292/moosh_moodle41_2023010400.zip"; \
# 	unzip moosh.zip; \
# 	ln -s /usr/src/moosh/moosh.php /usr/local/bin/moosh; \
# 	rm moosh.zip

RUN	echo "Installing unoconv"; \
	# apt-get update && apt-get install -y; \
	apt-get update; \
	apt-cache policy unoconv; \
	apt-get -y install unoconv; \
	mkdir /var/www/.config; \
	chown -R www-data:www-data /var/www/.config

RUN	echo "Installing expect"; \
	apt-get update; \
	apt-get -y install expect

RUN curl -o dockerize.tar.gz -fSL "https://github.com/jwilder/dockerize/releases/download/${DOCKERIZE_VERSION}/dockerize-linux-amd64-${DOCKERIZE_VERSION}.tar.gz"; \
	tar -xf dockerize.tar.gz -C /usr/local/bin; \
	rm dockerize.tar.gz

COPY moodle-config.php /usr/src/moodle/config.php

COPY docker-entrypoint.sh /usr/local/bin/

# remove plugins installation better a init dir where scripts can be executed if exists, from entrypoint
# COPY plugins-config.sh /usr/local/bin/plugins-config.sh
# COPY plugins /usr/src/

# Fix the original permissions of /tmp, the PHP default upload tmp dir.


RUN chmod 777 /tmp && chmod +t /tmp ;\
	chown -R www-data:www-data -R /usr/src/moodle ;\
	chown www-data:www-data /var/www # moosh uses it
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["php-fpm"]
