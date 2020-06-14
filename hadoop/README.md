 # Entorno Hadoop basado en Docker

## 1.Introduccion

###1. Versiones

- Sistema Operativo: CentOS 6
- Java: OpenJDK 8
- Hadoop: 2.7.2
- Spark: 2.1.0
- Hive: 2.1.1
- docker-compose para adminisrar las imagenes y contenedores
- Todos los paquetes binarios de software se descargan a través de Internet.

###2. Dependencias

- Docker
- Docker Compose

## 2.Como Usar

###1. Construir Imagenes
En el directorio raíz del proyecto (que contiene el archivo docker-compose-build-all.yml), 
ejecutar los siguientes comandos:	

- Descargar MySQL

`docker pull mysql:5.7`

- Descargar centos 

`docker pull centos:6`

- Cree un sistema operativo básico y un entorno OpenJDK, incluidos CentOS 6 y OpenJDK 8
    
`docker-compose -f docker-compose-build-all.yml build os-jvm`

- Construir entorno Hadoop

`docker-compose -f docker-compose-build-all.yml build hadoop`

- Construir entorno Hive

`docker-compose -f docker-compose-build-all.yml build hive`

- Construir entorno Spark

`docker-compose -f docker-compose-build-all.yml build spark`

###2. Iniciar y Detener el Cluster

Utilizar archivo docker-compose.yml en el directorio raiz del proyecto. 
En este archivo se configuro un cluster Spark que consta de 3 nodos esclavos y 1 nodo maestro 

- Inicializar el Cluster

<pre><code>
#[Crear contenedor]
docker-compose up -d
#[Formatear HDFS. Antes de iniciar el clúster por primera vez, debe formatear HDFS;
 cada vez que inicie el clúster, no necesita formatear HDFS nuevamente]
docker-compose exec spark-master hdfs namenode -format
#[Inicialice la base de datos de Hive. Ejecute solo una vez antes de iniciar el clúster por primera vez]
docker-compose exec spark-master schematool -dbType mysql -initSchema
# [Empaquete los archivos jar relacionados con Spark y guárdelos en el directorio / code, denominado spark-libs.jar]
docker-compose exec spark-master jar cv0f /code/spark-libs.jar -C /root/spark/jars/ .
# [Iniciar HDFS]
docker-compose exec spark-master start-dfs.sh
# [Crear / usuario / spark / share / lib / directorio en HDFS]
docker-compose exec spark-master hadoop fs -mkdir -p /user/spark/share/lib/
# [Cargue el archivo /code/spark-libs.jar en el directorio /user/spark/share/lib/ en HDFS]
docker-compose exec spark-master hadoop fs -put /code/spark-libs.jar /user/spark/share/lib/
# [Cerrar HDFS]
docker-compose exec spark-master stop-dfs.sh
</code></pre>

- Inicie el proceso del clúster y ejecútelo en este orden:

<pre><code>
#[Iniciar HDFS]
docker-compose exec spark-master start-dfs.sh
#[Iniciar YARN]
docker-compose exec spark-master start-yarn.sh
#[Iniciar Spark]
docker-compose exec spark-master start-all.sh
</code></pre>

- Detenga el clúster de Spark y ejecute:

<pre><code>
#[Detener Spark]
docker-compose exec spark-master stop-all.sh
#[Detener YARN]
docker-compose exec spark-master stop-yarn.sh
#[Detener HDFS]
docker-compose exec spark-master stop-dfs.sh
#[Detener el Contenedor]
docker-compose down</code></pre>


###3. Como usar el cluster 

El esquema de distribución es: 1 Nodo maestro y 3 Nodos esclavos.
La expansión del clúster se puede lograr ajustando el archivo de configuración docker-compose y el archivo de configuración del software correspondiente.

Se empaqueta como un archivo jar y luego se coloca en el directorio ./volume/code/ debajo del directorio raíz del proyecto. El directorio de codigo se montará bajo la ruta /codigo del nodo maestro cuando se inicie el clúster.
Pruebas para validar que el cluster levanto directorio: volume/code/tests/mapreduce-test. Después de iniciar el clúster e iniciar cada proceso de servicio. Ejecute la siguiente instrucción para ingresar al entorno de línea de comando del nodo maestro:
<pre><code>docker-compose exec spark-master /bin/bash
</code></pre>

Se pueden enviar jobs agregamndo el jar en: /code 

jps 
cd /code/tests/mapreduce-test/
hadoop jar wordcount.jar /in /output


