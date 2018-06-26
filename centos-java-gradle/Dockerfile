FROM centos:7.4.1708

RUN yum -y update

# 외부 레파지토리(EPEL, Remi)을 추가
RUN yum -y install epel-release
RUN yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm

RUN mkdir -p /home/in2020/springboot-init/

# java, gradle
WORKDIR /home/in2020/download
RUN yum -y install java-1.8.0-openjdk wget unzip which
RUN wget https://services.gradle.org/distributions/gradle-4.8.1-bin.zip
RUN mkdir /opt/gradle
RUN unzip -d /opt/gradle gradle-4.8.1-bin.zip

WORKDIR /home/in2020/springboot-init/

CMD bash