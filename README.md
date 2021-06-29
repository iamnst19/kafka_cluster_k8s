# kafka_cluster_k8s

## Steps to create the cluster

### Create Namespace:
```kubectl apply -f namespace.yml```

### Create PV:
```kubectl apply -f pv.yml```

### Adding helm Repo:
```helm repo add incubator https://charts.helm.sh/incubator```

### Export the chart values in a file:
```curl https://raw.githubusercontent.com/helm/charts/master/incubator/kafka/values.yaml > values.yaml```

### Then install the chart:
```helm install kafka-demo --namespace kafka incubator/kafka -f values.yaml --debug```

### Finally Testing the Kafka Cluster

```kubectl apply -f testclient```

### Then, using the testclient, we create the first topic, which we are going to use to post messages:
```kubectl -n kafka exec -ti testclient -- ./bin/kafka-topics.sh --zookeeper kafka-demo-zookeeper:2181 --topic messages --create --partitions 1 --replication-factor 1```

**output**
```Created topic "messages".```

### Here we need to use the correct hostname for zookeeper cluster and the topic configuration. Next, verify that the topic exists:

```kubectl -n kafka exec -ti testclient -- ./bin/kafka-topics.sh --zookeeper kafka-demo-zookeeper:2181 --list```

**output**
```Messages```

### Now we can create one consumer and one producer instance so that we can send and consume messages.

#### First create one or two listeners, each on its own shell:

```kubectl -n kafka exec -ti testclient -- ./bin/kafka-console-consumer.sh --bootstrap-server kafka-demo:9092 --topic messages --from-beginning```

## Then create the producer session and type some messages. You will be able to see them propagate to the consumer sessions:
```kubectl -n kafka exec -ti testclient -- ./bin/kafka-console-producer.sh --broker-list kafka-demo:9092 --topic messages```
