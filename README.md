# StormTopology-AuditActiveLogins-Elasticsearch

## Description

AuditActiveLogins is a test storm topology for learning purpose.

It counts the User Logins and User Logouts from an audit.log lines, readed from Kafka and insert/update the information in a Elasticsearch.

## Storm topology description

The topology has the following Components:  
  KafkaSpout -> AuditParserBolt -> AuditLoginsCounterBolt -> ElasticsearchBolt
  
### KafkaSpout: 
Connects to a Kafka topic and get the audit lines (previously inserted with flume)  
This spout is based on https://github.com/wurstmeister/storm-kafka-0.8-plus

### AuditParserBolt
Parse the audit line to extract all the information, and insert it in a HashMap structure

### AuditLoginsCounterBolt
Treat the audit line information and pass it to HbaseBolt for insert/update a row.  
In case of user login line, insert a new row with node|user rowkey and counter set to 1 or update an existing row incrementing the counter 1 unit.  
In case of user logout decrement the counter 1 unit (checking previously if the row exist and its counter is greater than zero)

### ElasticsearchBolt
Is the responsible of put/update information in Elasticsearch
This bolt is based on https://github.com/hmsonline/storm-elastic-search
  
## Compilation
  
```
  ./compile.sh
```  

## Config topology
```
# MANDATORY PROPERTIES

# zookeeper hosts and ports (eg: localhost:2181)
zookeeper.hosts=

# kafka topic for read messages
kafka.topic=

# Elasticsearch properties
elasticsearch.host=
elasticsearch.port=
elasticsearch.cluster.name=
elasticsearch.index=
elasticsearch.type=

# OPTIONAL PROPERTIES

# Numbers of workers to parallelize tasks (default 2)
# storm.workers.number=

# Numbers of max task for topology (default 2)
#storm.max.task.parallelism=

# Storm topolgy execution mode (local or cluster, default local)
#storm.execution.mode=

# Storm Topology Name (default AuditActiveLoginsCount)
#storm.topology.name=

# Storm batch emmit interval (default 2000)
#storm.topology.batch.interval.miliseconds

# Time of topology execution, in miliseconds (only in local mode, default 20000)
#storm.local.execution.time=


# CLUSTER PROPERTIES:
# Storm Nimbus host (default localhost)
# storm.nimbus.host=

# Storm Nimbus port (default 6627)
# storm.nimbus.port
```
  
## Run topology
```
   ./run.sh
```

### Storm dependencies

Some libraries are required int storm lib directory:
```
  storm-kafka-0.9.2-incubating.jar
  storm-elasticsearch-0.1.3.jar
```

### Run\submit topology  
```
 ./run.sh
```

## See results
Open Kibana and see the results
  

