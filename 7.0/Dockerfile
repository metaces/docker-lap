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
    echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list && \
    apt-get -qq update && apt-get -qqy upgrade

# Install PHP7 with Xdebug (dev environment)
RUN apt-get -yqq install \
    php7.0 \
    php7.0-apcu \
    php7.0-bcmath \
    php7.0-bz2 \
    php7.0-curl \
    php7.0-dev \
    php7.0-dba \
    php7.0-dom \
    php7.0-enchant \
    php7.0-fpm \
    php7.0-gd \
    php7.0-gmp \
    php7.0-imap \
    php7.0-interbase \
    php7.0-imagick \
    php7.0-intl \
    php7.0-json \
    php7.0-ldap \
    php7.0-mbstring \
    php7.0-mcrypt \
    php7.0-memcache \
    php7.0-memcached \
    php7.0-mysql \
    php7.0-oauth \
    php7.0-odbc \
    php7.0-opcache \
    php7.0-pgsql \
    php7.0-pspell \
    php7.0-readline \
    php7.0-recode \
    php7.0-redis \
    php7.0-snmp \
    php7.0-soap \
    php7.0-solr \
    php7.0-sqlite3 \
    php7.0-ssh2 \
    php7.0-sybase \
    php7.0-tidy \
    php7.0-uploadprogress \
    php7.0-xml \
    php7.0-xmlrpc \
    php7.0-xsl \
    php7.0-zip \
    libapache2-mod-php7.0

# PHP Timezone
RUN \
    echo $TZ | tee /etc/timezone && \
    dpkg-reconfigure --frontend noninteractive tzdata && \
    echo "date.timezone = \"$TZ\";" > /etc/php/7.0/apache2/conf.d/timezone.ini && \
    echo "date.timezone = \"$TZ\";" > /etc/php/7.0/cli/conf.d/timezone.ini

# PHP xdebug
RUN \
    wget https://github.com/xdebug/xdebug/archive/master.zip && \
    unzip master.zip && \
    cd xdebug-master && \
    phpize7.0 && \
    ./configure --enable-xdebug && \
    make && make install && \
    echo "zend_extension=xdebug.so" >> /etc/php/7.0/mods-available/xdebug.ini && \
    cd .. && rm -fr xdebug-master master.zip

# Install manually xhprof
RUN \
    cd /tmp && \
    wget https://github.com/Yaoguais/phpng-xhprof/archive/master.zip && \
    unzip master.zip && \
    cd phpng-xhprof-master && \
    phpize7.0 && \
    ./configure --with-php-config=/usr/bin/php-config7.0 && \
    make && make install && \
    mv /usr/lib/php/20151012/phpng_xhprof.so /usr/lib/php/20151012/xhprof.so  && \
    echo "extension=xhprof.so" > /etc/php/7.0/mods-available/xhprof.ini && \
    echo "xhprof.output_dir=/var/www/xhprof" >> /etc/php/7.0/mods-available/xhprof.ini

# Install manually APC
RUN \
    echo "extension=apcu.so" > /etc/php/7.0/mods-available/apcu_bc.ini && \
    echo "extension=apc.so" >> /etc/php/7.0/mods-available/apcu_bc.ini

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
    phpize7.0 && \
    ./configure --with-oci8=instantclient,/usr/lib/oracle/12.2/client64/lib/ && \
    make && make install && \
    echo "extension=oci8.so" > /etc/php/7.0/mods-available/oci8.ini && \
    ln -s /etc/php/7.0/mods-available/oci8.ini /etc/php/7.0/apache2/conf.d/oci8.ini

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
    php7.0 \
    mpm_prefork \
    reqtimeout \
    rewrite \
    setenvif \
    status \
    ssl

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

EXPOSE 80 443

CMD ["apache2-foreground"]
