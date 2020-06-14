FROM joseangelramos/os-jvm:centos6-openjdk8

MAINTAINER joseangelramos <joseangelramos@gmail.com>

USER root

ENV HADOOP_HOME=/root/hadoop
ENV HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HADOOP_HOME/lib/native/:/root/protobuf-2.5.0/lib
ENV PATH=$PATH:/root/protobuf-2.5.0/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:.

# 1. Instalar OpenSSH, OpenSSL, bzip2-devel
# 2. Configure el inicio de sesión sin llave SSH
RUN yum install -y openssh openssh-server openssh-clients openssl openssl-devel bzip2-devel && \
    yum clean all && \
    ssh-keygen -t rsa -f ~/.ssh/id_rsa -P '' && \
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

ADD config/other/ssh_config /root/.ssh/config
RUN chmod 600 /root/.ssh/config && \
    chown root:root /root/.ssh/config

# Instalar Protocol Buffer, Hadoop
RUN wget https://archive.apache.org/dist/hadoop/common/hadoop-2.7.2/hadoop-2.7.2.tar.gz && \
	tar -xzvf hadoop-2.7.2.tar.gz -C /root/ && \
    mv /root/hadoop-2.7.2 $HADOOP_HOME && \
    rm -rf hadoop-2.7.2.tar.gz && \
    rm -rf $HADOOP_HOME/bin/*.cmd && \
    rm -rf $HADOOP_HOME/sbin/*.cmd && \
    rm -rf $HADOOP_HOME/sbin/*all* && \
    rm -rf $HADOOP_CONF_DIR/*.cmd && \
    rm -rf $HADOOP_CONF_DIR/*.template && \
    rm -rf $HADOOP_CONF_DIR/*.example && \
    wget https://github.com/protocolbuffers/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz && \
	tar -xzvf protobuf-2.5.0.tar.gz -C /root/ && \
	rm -rf protobuf-2.5.0.tar.gz

# Copiar archivo de configuración de variable de entorno
ADD config/other/bashrc /root/.bashrc

# Copiar archivo de configuración de Hadoop
ADD config/hadoop/* $HADOOP_HOME/etc/hadoop/

# Crear directorio HDFS, directorio de registro. Formato NameNode
#RUN mkdir -p /works/hadoop/dfs/name && \
#    mkdir -p /works/hadoop/dfs/data && \
#    mkdir -p /works/hadoop/dfs/namesecondary && \
#    mkdir $HADOOP_HOME/logs && \
#    $HADOOP_HOME/bin/hdfs namenode -format

CMD [ "sh", "-c", "service sshd start; bash"]