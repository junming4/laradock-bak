#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

ARG PHP_VERSION=${PHP_VERSION}
FROM php:${PHP_VERSION}-alpine

LABEL maintainer="Mahmoud Zalt <mahmoud@zalt.me>"

#RUN apk --update add apt-get

RUN apk --update add wget \
  curl \
  git \
  build-base \
  libmemcached-dev \
  libmcrypt-dev \
  libxml2-dev \
  zlib-dev \
  autoconf \
  cyrus-sasl-dev \
  libgsasl-dev \
  libmcrypt-dev \
  freetype-dev \
  libjpeg-turbo-dev \
  libpng-dev \
  supervisor

RUN docker-php-ext-install mysqli mbstring pdo pdo_mysql tokenizer xml pcntl gd
RUN pecl channel-update pecl.php.net && pecl install memcached mcrypt-1.0.1 && docker-php-ext-enable memcached

#Install SOAP package:
ARG INSTALL_SOAP=false
RUN if [ ${INSTALL_SOAP} = true ]; then \
    docker-php-ext-install soap \
;fi

#Install BCMath package:
ARG INSTALL_BCMATH=false
RUN if [ ${INSTALL_BCMATH} = true ]; then \
    docker-php-ext-install bcmath \
;fi

# Install PostgreSQL drivers:
ARG INSTALL_PGSQL=false
RUN if [ ${INSTALL_PGSQL} = true ]; then \
    apk --update add postgresql-dev \
        && docker-php-ext-install pdo_pgsql \
;fi

# Install ZipArchive:
ARG INSTALL_ZIP_ARCHIVE=false
RUN if [ ${INSTALL_ZIP_ARCHIVE} = true ]; then \
    apk --update add libzip-dev && \
    docker-php-ext-configure zip --with-libzip && \
    # Install the zip extension
    docker-php-ext-install zip \
;fi


ARG INSTALL_IMAGEMAGICK=false

RUN if [ ${INSTALL_IMAGEMAGICK} = true ]; then \
    curl -L -o /tmp/ImageMagick.zip https://codeload.github.com/ImageMagick/ImageMagick/zip/master  && \
    unzip -d /tmp/ /tmp/ImageMagick.zip \
    && cd /tmp/ImageMagick-master \
    && ./configure --prefix=/usr/local/imagemagick --with-png=yes --with-jpg=yes \
    && make && make install \
    && rm -rf /tmp/ImageMagick* \
    && printf "\n" | pecl install -o -f imagick \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable imagick \
;fi

###########################################################################
# MongoDB:
###########################################################################

ARG INSTALL_MONGO=true

RUN if [ ${INSTALL_MONGO} = true ]; then \
    # Install the mongodb extension
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      pecl install mongo && \
      echo "extension=mongo.so" >> /usr/local/etc/php/conf.d/mongo.ini \
     # ln -s /usr/local/etc/php/conf.d/mongo.ini /usr/local/etc/php/conf.d/30-mongo.ini \
    ;fi && \
    pecl install mongodb && \
    echo "extension=mongodb.so" >> /usr/local/etc/php/conf.d/mongodb.ini \
    #ln -s /usr/local/etc/php/conf.d/mongodb.ini /usr/local/etc/php/conf.d/30-mongodb.ini \
;fi

ARG INSTALL_SWOOLE=true

RUN if [ ${INSTALL_SWOOLE} = true ]; then \
    # Install Php Swoole Extension
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      pecl -q install swoole-2.0.11; \
    else \
      if [ $(php -r "echo PHP_MINOR_VERSION;") = "0" ]; then \
        pecl install swoole-2.2.0; \
      else \
        pecl install swoole; \
      fi \
    fi && \
    echo "extension=swoole.so" >> /usr/local/etc/php/conf.d/swoole.ini \
    #ln -s /etc/php/${LARADOCK_PHP_VERSION}/mods-available/swoole.ini /etc/php/${LARADOCK_PHP_VERSION}/cli/conf.d/20-swoole.ini \
;fi



###########################################################################
# MySQL Client:
###########################################################################

ARG INSTALL_MYSQL_CLIENT=false

RUN if [ ${INSTALL_MYSQL_CLIENT} = true ]; then \
    apk add --update mysql-client mariadb-dev \
;fi


###########################################################################
# PYTHON:
###########################################################################

ARG INSTALL_PYTHON=false

RUN if [ ${INSTALL_PYTHON} = true ]; then \
  apk add --update  python py-pip python-dev build-base g++ gcc libxslt-dev \
  && python -m pip install --upgrade pip  \
  && python -m pip install --upgrade pymongo \
  && python -m pip install --upgrade virtualenv \
  && python -m pip install --upgrade selenium \
  #&& python -m pip install --upgrade Pillow \
  && python -m pip install --upgrade requests \
  && python -m pip install --upgrade pyquery \
  #&& python -m pip install --upgrade pytesseract \
  #&& python -m pip install --upgrade MySQL-python \
  && python -m pip install --upgrade redis \
  #&& python -m pip install --upgrade captcha \
  #&& python -m pip install https://download.pytorch.org/whl/cpu/torch-1.0.1.post2-cp27-cp27mu-linux_x86_64.whl \
  #&& python -m pip install --upgrade torchvision \
  #&& python -m pip install --upgrade opencv-python \
  && python -m pip install --upgrade numpy \

;fi

ARG INSTALL_GD=true

#RUN mkdir -p /usr/local/man/man1

#RUN if [ ${INSTALL_GD} = true ]; then \
    #curl -L -o /tmp/libpng-1.6.37.tar.xz https://nchc.dl.sourceforge.net/project/libpng/libpng16/1.6.37/libpng-1.6.37.tar.xz  && \
    #tar -xvJf /tmp/libpng-1.6.37.tar.xz -C /tmp/ \
    #&& cd /tmp/libpng-1.6.37 \
    #&& ./configure \
    #&& make && make install \
    #&& rm -rf /tmp/libpng* \ &&
    #/tmp/jpegsrc.v6b.tar.gz https://jaist.dl.sourceforge.net/project/libjpeg/libjpeg/6b/jpegsrc.v6b.tar.gz
    #curl -L -o /tmp/jpegsrc.v6b2.tar.gz https://jpegclub.org/support/files/jpegsrc.v6b2.tar.gz \
    #&& tar -zxvf /tmp/jpegsrc.v6b2.tar.gz -C /tmp/ \
    #&& cd /tmp/jpeg-6b2 \
    #&& ./configure --enable-shared --enable-static \
    #&& make && make install \
    #&& rm -rf /tmp/jpeg* \

#;fi

#RUN docker-php-ext-install gd

RUN rm /var/cache/apk/* \
    && mkdir -p /var/www

#
#--------------------------------------------------------------------------
# Optional Supervisord Configuration
#--------------------------------------------------------------------------
#
# Modify the ./supervisor.conf file to match your App's requirements.
# Make sure you rebuild your container with every change.
#

COPY supervisord.conf /etc/supervisord.conf

ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c",  "/etc/supervisord.conf"]

#
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# If you need to modify this image, feel free to do it right here.
#
    # -- Your awesome modifications go here -- #

#
#--------------------------------------------------------------------------
# Check PHP version
#--------------------------------------------------------------------------
#

RUN php -v | head -n 1 | grep -q "PHP ${PHP_VERSION}."

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

WORKDIR /etc/supervisor/conf.d/
