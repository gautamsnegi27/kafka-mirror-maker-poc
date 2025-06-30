# kafka-mirror-maker-poc

## Terminal 1
```bash
# Start Zookeeper (Port 2181)
bin/zookeeper-server-start.sh config/zookeeper.properties

# In a new tab, start Kafka (Port 9092)
bin/kafka-server-start.sh config/server.properties \
  --override broker.id=1 \
  --override listeners=PLAINTEXT://:9092 \
  --override log.dirs=/tmp/kafka-prod-logs
```

## Terminal 2
```bash
# Start Zookeeper (Port 2182)
bin/zookeeper-server-start.sh config/zookeeper.properties \
  --override clientPort=2182 \
  --override dataDir=/tmp/zookeeper-uat

# In a new tab, start Kafka (Port 9093)
bin/kafka-server-start.sh config/server.properties \
  --override broker.id=2 \
  --override listeners=PLAINTEXT://:9093 \
  --override log.dirs=/tmp/kafka-uat-logs \
  --override zookeeper.connect=localhost:2182
```


## Start MM2
```shell
bin/connect-mirror-maker.sh config/mm2.properties
``` 

## Create Topic & Send Messages (PROD)
```bash
bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic truck-lane
echo "msg1\nmsg2\nmsg3" | bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic truck-lane
```

## Check Messages in UAT
```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9093 --topic truck-lane --from-beginning
```