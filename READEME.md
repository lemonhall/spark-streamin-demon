1、下载Spark
===========
http://d3kbcqa49mib13.cloudfront.net/spark-1.0.1-bin-hadoop2.tgz


2、下载Kafka
===========
参考：http://www.douban.com/note/383894128/
http://kafka.apache.org/downloads.html
建议是下载基于2.9.2的scala版本的bin，来玩
https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.1.1/kafka_2.9.2-0.8.1.1.tgz

wget http://mirrors.hust.edu.cn/apache/kafka/0.8.1.1/kafka_2.9.2-0.8.1.1.tgz

3、启动Kafka
===========
启动zk
bin/zookeeper-server-start.sh config/zookeeper.properties

启动kafka本身
bin/kafka-server-start.sh config/server.properties

创建一个topic
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 10 --topic test


4、生产者：
========
https://github.com/SOHU-Co/kafka-node
npm install kafka-node

生产者：
var kafka = require('kafka-node'),
    HighLevelProducer = kafka.HighLevelProducer,
    client = new kafka.Client("127.0.0.1:2181"),
    producer = new HighLevelProducer(client);

var m = 0;

producer.on('ready', function () {
    setInterval(function(){
        producer.send( [ { topic:'test', messages : "fly me to the moon" } ], function (err, data) {
                if(!err){
                        console.log(data);
                }else{
                        console.log(err);
                }

        });
    },1000);
});

5、消费者
========
见源代码

6、启动程序
=========
#!/usr/bin/env bash

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

SCALA_VERSION=2.10

FWDIR="$(cd `dirname $0`/..; pwd)"
export SPARK_HOME="$FWDIR"
EXAMPLES_DIR="/Users/nixiephoebe/spark"

if [ -n "$1" ]; then
  EXAMPLE_CLASS="$1"
  shift
else
  echo "Usage: ./bin/run-example <example-class> [example-args]" 1>&2
  echo "  - set MASTER=XX to use a specific master" 1>&2
  echo "  - can use abbreviated example class name (e.g. SparkPi, mllib.LinearRegression)" 1>&2
  exit 1
fi

export SPARK_EXAMPLES_JAR=`ls "$EXAMPLES_DIR"/target/scala-$SCALA_VERSION/spark-examples*.jar`

if ［ -z $SPARK_EXAMPLES_JAR ］; then
  echo "Failed to find Spark examples assembly in $FWDIR/lib or $FWDIR/examples/target" 1>&2
  echo "You need to build Spark before running this program" 1>&2
  exit 1
fi

EXAMPLE_MASTER=${MASTER:-"local[*]"}

if ［ ! $EXAMPLE_CLASS == org.apache.spark.examples* ］; then
  EXAMPLE_CLASS="$EXAMPLE_CLASS"
fi

"$FWDIR"/bin/spark-submit \
  --master $EXAMPLE_MASTER \
  --class $EXAMPLE_CLASS \
  "$SPARK_EXAMPLES_JAR" \
  "$@"





