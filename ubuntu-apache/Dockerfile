FROM ubuntu:18.04

RUN apt-get update
RUN apt-get -y upgrade

# Reference http://twibap.tistory.com/9

# Compile tools
RUN apt-get install -y build-essential
RUN apt-get install -y libexpat1-dev

# Install wget
RUN apt-get install -y wget

# Make Downloads directory
WORKDIR /root
RUN mkdir Downloads

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

# RUN yum -y install httpd
# RUN yum -y install --enablerepo=remi,remi-php72 php php-cli php-common php-devel php-fpm php-gd php-mbstring php-mysqlnd php-pdo php-pear php-pecl-apcu php-soap php-xml php-xmlrpc php-xdebug
# RUN yum -y install zip unzip

# # composer 설치
# RUN curl -sS https://getcomposer.org/installer | php
# RUN mv composer.phar /usr/local/bin/composer

# # php log 설정
# WORKDIR /var/www
# RUN mkdir logs
# RUN chmod 777 logs

# CMD ["/usr/local/apache2/bin/apachectl ,"start"]