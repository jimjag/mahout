#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#


FROM openjdk:8-alpine

ARG spark_uid=185

# Before building the mahout docker image, we must build a spark distrobution following
# the instructions in http://spark.apache.org/docs/latest/building-spark.html.
# this Dockerfile will build the Spark version 2.4.3 against Scala 2.12.
# docker build -t mahout:latest -f resource_managers/docker/kubernetes/src/main/dockerfiles/Dockerfile .


RUN set -ex && \
    apk upgrade --no-cache && \
    ln -s /lib /lib64 && \
    apk add --no-cache bash tini libc6-compat linux-pam krb5 krb5-libs nss curl openssl && \
    mkdir -p /opt/mahout && \
    mkdir -p /opt/mahout/examples && \
    mkdir -p /opt/mahout/work-dir && \
    mkdir -p /opt/spark && \
    export MAHOUT_HOME=. && \
    mkdir -p $MAHOUT_HOME/spark-build && \
    export MAVEN_OPTS="-Xmx2g -XX:ReservedCodeCacheSize=512m" && \
    export SPARK_HOME=$MAHOUT_HOME/spark-build/ && \
    export SPARK_SRC_URL="https://www.apache.org/dyn/closer.lua/spark/spark-2.4.3/spark-2.4.3.tgz" && \
    export SPARK_SRC_SHA256="3EAEA3B0A81A717BB43CE6EE0BB2C3B8351EF080DB9499AF66F9F22C8A18D38C5E1426CBFEF04AFD2A4002ACE5B28A6BEACBCE4E5E42506F4FD270B05D0DB379" && \
    curl  -LfsS $SPARK_SRC_URL -o $SPARK_HOME/spark-2.4.3.tgz  && \
    echo "${SPARK_SRC_SHA256} ${SPARK_HOME}/spark-2.4.3.tgz" | sha512sum -c - && \
    $SPARK_HOME/dev/change-scala-version.sh 2.12 && \
    $SPARK_HOME/build/mvn -Pkubernetes -Pscala-2.12 -DskipTests clean package && \
    touch /opt/mahout/RELEASE && \
    rm /bin/sh && \
    ln -sv /bin/bash /bin/sh && \
    echo "auth required pam_wheel.so use_uid" >> /etc/pam.d/su && \
    chgrp root /etc/passwd && chmod ug+rw /etc/passwd


COPY lib /opt/mahout/lib
COPY bin /opt/mahout/bin
COPY resource-managers/kubernetes/docker/src/main/dockerfiles/entrypoint.sh /opt/
COPY examples /opt/mahout/examples

COPY spark-build/jars /opt/spark/jars
COPY spark-build/bin /opt/spark/bin
COPY spark-build/sbin /opt/spark/sbin
COPY spark-build/kubernetes/tests /opt/spark/tests
COPY spark-build/data /opt/spark/data

ENV MAHOUT_HOME /opt/mahout
ENV SPARK_HOME /opt/spark



WORKDIR /opt/mahout/work-dir
RUN chmod g+w /opt/mahout/work-dir

ENTRYPOINT [ "/opt/entrypoint.sh" ]

# Specify the User that the actual main process will run as
USER ${spark_uid}