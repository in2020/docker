FROM ubuntu:18.04

RUN apt-get update
RUN apt-get -y upgrade

# Reference http://twibap.tistory.com/9, http://php.net/manual/kr/install.php

# Compile tools
RUN apt-get install -y build-essential
RUN apt-get install -y libexpat1-dev
RUN apt-get install -y libxml2-dev

# Network tools
#RUN apt-get install -y network-manager
RUN apt-get install -y net-tools
#RUN apt-get install -y openssh-server
RUN apt-get install -y ufw
RUN apt-get install -y dnsutils

# Install wget
RUN apt-get install -y wget

# Make Downloads directory
WORKDIR /root
RUN mkdir Downloads

## Apache
# Download Apache & extraction
WORKDIR /root/Downloads
RUN wget http://mirror.navercorp.com/apache//httpd/httpd-2.4.37.tar.gz
RUN tar zxvf httpd-2.4.37.tar.gz

# Download APR, APR-util & extraction
WORKDIR /root/Downloads
RUN wget http://apache.mirror.cdnetworks.com//apr/apr-1.6.5.tar.gz
RUN wget http://apache.mirror.cdnetworks.com//apr/apr-util-1.6.1.tar.gz
RUN tar zxvf apr-1.6.5.tar.gz
RUN tar zxvf apr-util-1.6.1.tar.gz

# Download pcre & extraction
WORKDIR /root/Downloads
RUN wget https://ftp.pcre.org/pub/pcre/pcre-8.41.tar.gz
RUN tar zxvf pcre-8.41.tar.gz

# Install APR
WORKDIR /root/Downloads/apr-1.6.5
RUN ./configure -q
RUN make
RUN make install

# Install APR-util
WORKDIR /root/Downloads/apr-util-1.6.1
RUN ./configure -q --with-apr=/usr/local/apr
RUN make
RUN make install

# Install PCRE
WORKDIR /root/Downloads/pcre-8.41
RUN ./configure -q
RUN make && make install

# Install Apache
WORKDIR /root/Downloads/httpd-2.4.37
RUN ./configure -q
RUN make && make install

# Run apache server 
RUN /sbin/ldconfig
# RUN /usr/local/apache2/bin/apachectl start

## PHP
# Download PHP & extraction
WORKDIR /root/Downloads
RUN wget http://jp2.php.net/get/php-7.3.1.tar.gz/from/this/mirror -O ./php-7.3.1.tar.gz
RUN tar zxvf php-7.3.1.tar.gz

# Install PHP
WORKDIR /root/Downloads/php-7.3.1
RUN ./configure -q
RUN make
RUN make install

# # composer 설치
# RUN curl -sS https://getcomposer.org/installer | php
# RUN mv composer.phar /usr/local/bin/composer

# # php log 설정
# WORKDIR /var/www
# RUN mkdir logs
# RUN chmod 777 logs

# CMD ["/usr/local/apache2/bin/apachectl ,"start"]