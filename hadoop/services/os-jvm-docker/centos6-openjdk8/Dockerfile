FROM centos:6

MAINTAINER joseangelramos <joseangelramos@gmail.com>

USER root

ENV JAVA_HOME=/usr/lib/jvm/java-openjdk

ENV PATH=$PATH:$JAVA_HOME/bin:.

# Instalar OpenJDK, wget, rsync
RUN yum install -y java-1.8.0-openjdk-devel wget rsync && yum clean all

CMD ["/bin/bash"]