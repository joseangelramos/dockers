FROM joseangelramos/hive:2.1.1

MAINTAINER joseangelramos <joseangelramos@gmail.com>

USER root

# Establecer variables de entorno
ENV SPARK_HOME=/root/spark
ENV PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin:.

# Instalar spark 2.1.0
RUN wget http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-without-hadoop.tgz && \
    tar -xzvf spark-2.1.0-bin-without-hadoop.tgz -C /root/ && \
    mv /root/spark-2.1.0-bin-without-hadoop $SPARK_HOME && \
    rm -rf spark-2.1.0-bin-without-hadoop.tgz && \
    rm -rf $SPARK_HOME/bin/*.cmd && \
    rm -rf $SPARK_HOME/sbin/*.cmd && \
    rm -rf $SPARK_HOME/ec2 && \
    rm -rf $SPARK_HOME/examples && \
    rm -rf $SPARK_HOME/lib/spark-examples-*

#Copia el archivo de configuración de Spark
COPY config/spark/* $SPARK_HOME/conf/

CMD [ "sh", "-c", "service sshd start; bash"]