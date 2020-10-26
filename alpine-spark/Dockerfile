#Hadoop
FROM alpine as alpine-hadoop

ENV HADOOP_VERSION 3.2.0
ENV HADOOP_HOME /usr/hadoop-$HADOOP_VERSION
ENV HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
ENV PATH $PATH:$HADOOP_HOME/bin

ENV HADOOP_CLASSPATH="$HADOOP_HOME/etc/hadoop/*:$HADOOP_HOME/share/hadoop/common/lib/*:$HADOOP_HOME/share/hadoop/common/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/hdfs/lib/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/yarn/lib/*:$HADOOP_HOME/share/hadoop/yarn/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*:$HADOOP_HOME/share/hadoop/mapreduce/*:$HADOOP_HOME/share/hadoop/tools/lib/*"

RUN wget \
 --no-check-certificate \
  "http://archive.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz" -O hadoop.tar.gz \
 && gunzip hadoop.tar.gz \
 && tar -C /usr/ -xf hadoop.tar \
 && rm hadoop.tar \
 && rm -rf $HADOOP_HOME/share/doc \
 && chown -R root:root $HADOOP_HOME \
 && mkdir -p /usr/hadoop-deps \
 && for i in $(echo $HADOOP_CLASSPATH | tr ":" "\n"); do cp $i /usr/hadoop-deps/ -r; done


## SPARK
FROM frolvlad/alpine-miniconda3 as miniconda-spark

ENV SPARK_VERSION 2.4.1
ENV SPARK_PACKAGE spark-${SPARK_VERSION}-bin-without-hadoop
ENV SPARK_HOME /usr/spark-${SPARK_VERSION}
ENV PATH $PATH:$SPARK_HOME/bin

ENV HADOOP_VERSION 3.2.0
ENV HADOOP_HOME /usr/hadoop-$HADOOP_VERSION
ENV HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
ENV PATH $PATH:$HADOOP_HOME/bin

ENV SPARK_DIST_CLASSPATH="/usr/hadoop-deps/*"
ENV PATH $PATH:${SPARK_HOME}/bin

COPY --from=alpine-hadoop /usr/hadoop-deps/* /usr/hadoop-deps/

RUN wget \
  --no-check-certificate \
  "https://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/${SPARK_PACKAGE}.tgz" -O spark.tar.gz \
 && gunzip spark.tar.gz \
 && tar -C /usr/ -xf spark.tar \
 && rm spark.tar \
 && mv /usr/$SPARK_PACKAGE $SPARK_HOME \
 && chown -R root:root $SPARK_HOME \
 && rm /usr/hadoop-deps/avro-1.7.7.jar \
 && wget https://repo1.maven.org/maven2/org/apache/avro/avro/1.8.2/avro-1.8.2.jar -P /usr/hadoop-deps/ \
 && wget https://repo1.maven.org/maven2/org/apache/spark/spark-avro_2.11/${SPARK_VERSION}/spark-avro_2.11-${SPARK_VERSION}.jar -P /usr/hadoop-deps/

##APP
FROM miniconda-spark 

ADD ./requirements.txt .

RUN pip install --no-cache-dir -e $SPARK_HOME/python/ && \
    pip install --no-cache-dir -r requirements.txt && \
    rm requirements.txt && \
    apk add --update git bash openjdk8 openssh-client && \
    rm -rf /var/cache/apk/* && \
    rm -rf /root/.cache

ADD spark-defaults.conf ${SPARK_HOME}/conf/
ADD spark-env.sh ${SPARK_HOME}/conf/
RUN chmod 777 ${SPARK_HOME}/conf/spark-env.sh

