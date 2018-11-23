FROM debian:stretch-slim

#
# Step 1: Installation
#

# Set frontend. We'll clean this later on!
ENV DEBIAN_FRONTEND noninteractive

# Locale
ENV LOCALE pt_BR.UTF-8

# PHP Timezone
ENV TZ=America/Maceio

# PHP default_charset
ENV DEFAULT_CHARSET=

# Set repositories
RUN \
    echo "deb http://ftp.br.debian.org/debian/ stretch main contrib non-free" > /etc/apt/sources.list && \
    echo "deb-src http://ftp.br.debian.org/debian/ stretch main contrib non-free" >> /etc/apt/sources.list && \
    echo "deb http://security.debian.org/debian-security stretch/updates main contrib non-free" >> /etc/apt/sources.list && \
    echo "deb-src http://security.debian.org/debian-security stretch/updates main contrib non-free" >> /etc/apt/sources.list && \
    apt-get -qq update && apt-get -qqy upgrade

# Install some basic tools needed for deployment
RUN apt-get -yqq install \
    apt-utils \
    build-essential \
    debconf-utils \
    debconf \
    mysql-client \
    locales \
    curl \
    wget \
    unzip \
    patch \
    rsync \
    vim \
    openssh-client \
    git \
    bash-completion \
    locales \
    libjpeg-turbo-progs libjpeg-progs \
    pngcrush optipng \
    libaio1

# Install locale
RUN \
    sed -i -e "s/# $LOCALE/$LOCALE/" /etc/locale.gen && \
    echo "LANG=$LOCALE">/etc/default/locale && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=$LOCALE

# Configure Sury sources
# @see https://www.noobunbox.net/serveur/auto-hebergement/installer-php-7-1-sous-debian-et-ubuntu
RUN \
    apt-get -yqq install apt-transport-https lsb-release ca-certificates && \
    wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
    echo "deb https://packages.sury.org/php/ stretch main" > /etc/apt/sources.list.d/php.list && \
    apt-get -qq update && apt-get -qqy upgrade

# Install PHP7 with Xdebug (dev environment)
RUN apt-get -yqq install \
    php7.2 \
    php7.2-apcu \
    php7.2-bcmath \
    php7.2-bz2 \
    php7.2-curl \
    php7.2-dev \
    php7.2-dba \
    php7.2-dom \
    php7.2-enchant \
    php7.2-fpm \
    php7.2-gd \
    php7.2-gmp \
    php7.2-imap \
    php7.2-interbase \
    php7.2-imagick \
    php7.2-intl \
    php7.2-json \
    php7.2-ldap \
    php7.2-mbstring \
#    php7.2-mcrypt \
    php7.2-memcache \
    php7.2-memcached \
    php7.2-mysql \
    php7.2-oauth \
    php7.2-odbc \
    php7.2-opcache \
    php7.2-pgsql \
    php7.2-pspell \
    php7.2-readline \
    php7.2-recode \
    php7.2-redis \
    php7.2-snmp \
    php7.2-soap \
    php7.2-solr \
    php7.2-sqlite3 \
    php7.2-ssh2 \
    php7.2-sybase \
    php7.2-tidy \
    php7.2-uploadprogress \
    php7.2-xml \
    php7.2-xmlrpc \
    php7.2-xsl \
    php7.2-zip \
    libapache2-mod-php7.2

# PHP Timezone
RUN \
    echo $TZ | tee /etc/timezone && \
    dpkg-reconfigure --frontend noninteractive tzdata && \
    echo "date.timezone = \"$TZ\";" > /etc/php/7.2/apache2/conf.d/timezone.ini && \
    echo "date.timezone = \"$TZ\";" > /etc/php/7.2/cli/conf.d/timezone.ini

# PHP default charset
RUN sed -i -e "s/default_charset = \"UTF-8\"/default_charset = $DEFAULT_CHARSET/" /etc/php/7.2/apache2/php.ini

# PHP short tag
RUN sed -i -e "s/short_open_tag = Off/short_open_tag = On/" /etc/php/7.2/apache2/php.ini


# PHP xdebug
RUN \
    wget https://github.com/xdebug/xdebug/archive/master.zip && \
    unzip master.zip && \
    cd xdebug-master && \
    phpize7.2 && \
    ./configure --enable-xdebug && \
    make && make install && \
    echo "zend_extension=xdebug.so" >> /etc/php/7.2/mods-available/xdebug.ini && \
    cd .. && rm -fr xdebug-master master.zip

# Install manually xhprof
RUN \
    cd /tmp && \
    wget https://github.com/Yaoguais/phpng-xhprof/archive/master.zip && \
    unzip master.zip && \
    cd phpng-xhprof-master && \
    phpize7.2 && \
    ./configure --with-php-config=/usr/bin/php-config7.2 && \
    make && make install && \
    mv /usr/lib/php/20170718/phpng_xhprof.so /usr/lib/php/20170718/xhprof.so  && \
    echo "extension=xhprof.so" > /etc/php/7.2/mods-available/xhprof.ini && \
    echo "xhprof.output_dir=/var/www/xhprof" >> /etc/php/7.2/mods-available/xhprof.ini

# Install manually APC
RUN \
    echo "extension=apcu.so" > /etc/php/7.2/mods-available/apcu_bc.ini && \
    echo "extension=apc.so" >> /etc/php/7.2/mods-available/apcu_bc.ini

# Install SMTP.
RUN apt-get install -y ssmtp && \
    echo "FromLineOverride=YES" >> /etc/ssmtp/ssmtp.conf

# Install Oracle
ADD oracle/oracle-instantclient12.2-basic_12.2.0.1.0-2_amd64.deb /tmp
ADD oracle/oracle-instantclient12.2-devel_12.2.0.1.0-2_amd64.deb /tmp
RUN dpkg -i /tmp/oracle-instantclient12.2-basic_12.2.0.1.0-2_amd64.deb
RUN dpkg -i /tmp/oracle-instantclient12.2-devel_12.2.0.1.0-2_amd64.deb

ENV LD_LIBRARY_PATH /usr/lib/oracle/12.2/client64/lib/
ENV ORACLE_HOME /usr/lib/oracle/12.2/client64
RUN export LD_LIBRARY_PATH=$LD_LIBRARY_PATH
RUN export ORACLE_HOME=$ORACLE_HOME
RUN echo $ORACLE_HOME 

ADD oracle/oci8-2.1.8.zip /tmp

RUN echo $LD_LIBRARY_PATH && echo $ORACLE_HOME && \
    ln -s /usr/include/oracle/12.2/client64 $ORACLE_HOME/include && \
    cd /tmp && unzip oci8-2.1.8.zip -d /usr/lib/oracle/src && \
    cd /usr/lib/oracle/src/oci8-2.1.8 && \
    phpize7.2 && \
    ./configure --with-oci8=instantclient,/usr/lib/oracle/12.2/client64/lib/ && \
    make && make install && \
    echo "extension=oci8.so" > /etc/php/7.2/mods-available/oci8.ini && \
    ln -s /etc/php/7.2/mods-available/oci8.ini /etc/php/7.2/apache2/conf.d/oci8.ini

ADD oracle/php-src-PHP-7.2.11.zip /tmp

RUN echo $LD_LIBRARY_PATH && echo $ORACLE_HOME && \
    ln -s /usr/include/oracle/12.2/client64 $ORACLE_HOME/include && \
    cd /tmp && unzip php-src-PHP-7.2.11.zip -d /usr/lib/oracle/src && \
    cd /usr/lib/oracle/src/php-src-PHP-7.2.11/ext/pdo_oci/ && \
    phpize7.2 && \
    ./configure --with-pdo-oci=instantclient,/usr/lib/oracle/12.2/client64/lib/,12.2 && \
    make && make install && \
    echo "extension=pdo_oci.so" > /etc/php/7.2/mods-available/pdo_oci.ini && \
    ln -s /etc/php/7.2/mods-available/pdo_oci.ini /etc/php/7.2/apache2/conf.d/pdo_oci.ini

# Install Apache web server.
RUN apt-get -yqq install apache2

#
# Step 2: Configuration
#

# Enable uploadprogress, imagick, redis and solr.
RUN phpenmod uploadprogress imagick redis solr

# Disable by default apcu, apcu_bc, opcache, xdebug and xhprof. Use docker-compose.yml to add file.
RUN phpdismod apcu apcu_bc opcache xdebug xhprof

# Remove all sites enabled
RUN rm /etc/apache2/sites-enabled/*

# Configure needed apache modules and disable default site
RUN a2dismod mpm_event cgi # mpm_worker enabled.
RUN a2enmod \
    access_compat \
    actions \
    alias \
    auth_basic \
    authn_core \
    authn_file \
    authz_core \
    authz_groupfile \
    authz_host \
    authz_user \
    autoindex \
    dir \
    env \
    expires \
    filter \
    headers \
    mime \
    negotiation \
    php7.2 \
    mpm_prefork \
    reqtimeout \
    rewrite \
    setenvif \
    status \
    ssl

# Apache2 rewrite
RUN sed -i -e "s/AllowOverride None/AllowOverride All/g" /etc/apache2/apache2.conf

# Install composer (latest version) | prestissimo to speed up composer
RUN curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    composer global require "hirak/prestissimo:^0.3"

### Install DRUSH (latest stable) ###
# Run this in your terminal to get the latest DRUSH project version:
RUN composer global require drush/drush && \
    ~/.composer/vendor/bin/drush init

### Install DRUPAL CONSOLE (latest version) ###
# Run this in your terminal to get the latest project version:
RUN curl https://drupalconsole.com/installer -L -o drupal.phar && \
    mv drupal.phar /usr/local/bin/drupal && \
    chmod +x /usr/local/bin/drupal  && \
    drupal self-update

# Bash setup.
RUN echo ". /usr/share/bash-completion/bash_completion" >> ~/.bashrc && echo "alias ll='ls -lahs'" >> ~/.bashrc

#
# Step 3: Clean the system
#

# Cleanup some things.
RUN apt-get -q autoclean && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /tmp/*

#
# Step 4: Run
#

# Create 'luis' user like local machime user.
RUN useradd luis && usermod -G www-data -a luis

# Working dir
WORKDIR /var/www/html

# Volume for Apache2 data
VOLUME /var/www/html

COPY scripts/apache2-foreground /usr/bin/
RUN chmod +x /usr/bin/apache2-foreground

EXPOSE 80 443

CMD ["apache2-foreground"]
