FROM centos:7.4.1708

RUN yum -y update

RUN yum -y install net-tools

# 외부 레파지토리(EPEL, Remi)을 추가
RUN yum -y install epel-release
RUN yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

# apache기타 php패키지를 도입
RUN yum -y install httpd
RUN yum -y install --enablerepo=remi,remi-php72 php php-cli php-common php-devel php-fpm php-gd php-mbstring php-mysqlnd php-pdo php-pear php-pecl-apcu php-soap php-xml php-xmlrpc php-xdebug
RUN yum -y install zip unzip

# composer 설치
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

# php log 설정
WORKDIR /var/www
RUN mkdir logs
RUN chmod 777 logs

CMD ["/usr/sbin/httpd","-D","FOREGROUND"]


WORKDIR /var/www/html