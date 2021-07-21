# Kafka Metrics

> Resources:
>
> - [confluent.io: Apache Kafka Lag Monitoring at AppsFlyer](https://www.confluent.io/blog/kafka-lag-monitoring-and-metrics-at-appsflyer/)
> - [datadoghq.com: Monitoring Kafka performance metrics](https://www.datadoghq.com/blog/monitoring-kafka-performance-metrics/)
> - [medium.com: Monitoring Kafka with Kafka exporter + Prometheus + Grafana](https://danielmrosa.medium.com/monitoring-kafka-b97d2d5a5434)
> - [lightbend.com: Monitor Kafka Consumer Group Latency with Kafka Lag Exporter](https://www.lightbend.com/blog/monitor-kafka-consumer-group-latency-with-kafka-lag-exporter)
> - [logz.io: Monitoring Kafka in Production](https://logz.io/blog/monitoring-kafka-in-production/)

## Tooling

### kafka-exporter

- `kafka-exporter`: [https://github.com/danielqsj/kafka_exporter](https://github.com/danielqsj/kafka_exporter)
- `bitnami/kafka-exporter` Docker image: [https://github.com/bitnami/bitnami-docker-kafka-exporter](https://github.com/bitnami/bitnami-docker-kafka-exporter)

### Prometheus JMX Exporter

- `jmx_exporter`: [https://github.com/prometheus/jmx_exporter](https://github.com/prometheus/jmx_exporter)
- `bitnami/jmx-exporter` Docker image: [https://github.com/bitnami/bitnami-docker-jmx-exporter](https://github.com/bitnami/bitnami-docker-jmx-exporter)

## Observed Metrics

### Requests in/out per second

- Metrics:
  - `kafka_server_brokertopicmetrics_totalproducerequestspersec_count`
  - `kafka_server_brokertopicmetrics_totalfetchrequestspersec_count`
- Source: JMX Exporter

Requests in:

```plain
sum(rate(kafka_server_brokertopicmetrics_totalproducerequestspersec_count{app_kubernetes_io_instance="$broker", topic=~"$topic"}[5m])) by (topic)
```

Requests out:

```plain
sum(rate(kafka_server_brokertopicmetrics_totalfetchrequestspersec_count{app_kubernetes_io_instance="$broker", topic=~"$topic"}[5m])) by (topic)
```

### Offset per second

- Metrics:
  - `kafka_topic_partition_current_offset`
  - `kafka_consumergroup_current_offset`
- Source: kafka-exporter

Current Partition Offset:

```plain
sum(rate(kafka_topic_partition_current_offset{app_kubernetes_io_instance="$broker", topic=~"$topic"}[5m])) by (topic)
```

Current Consumer Offset:

```plain
sum(rate(kafka_consumergroup_current_offset{app_kubernetes_io_instance="$broker", topic=~"$topic"}[5m])) by (topic)
```

### Approximate Lag by Consumer Group

- Metrics:
  - `kafka_consumergroup_lag`
- Source: kafka-exporter

Approximate Lag by Consumer Group:

```plain
sum(kafka_consumergroup_lag{app_kubernetes_io_instance="$broker", topic=~"$topic"}) by (consumergroup, topic)
```

### Network in/out per second

- Metrics:
  - `kafka_server_brokertopicmetrics_bytesinpersec_count`
  - `kafka_server_brokertopicmetrics_bytesoutpersec_count`
- Source: JMX Exporter

Bytes in (MB):

```plain
sum(rate(kafka_server_brokertopicmetrics_bytesinpersec_count{app_kubernetes_io_instance="$broker", topic=~"$topic"}[5m]) / 1024^2) by (topic)
```

Bytes out (MB):

```plain
sum(rate(kafka_server_brokertopicmetrics_bytesoutpersec_count{app_kubernetes_io_instance="$broker", topic=~"$topic"}[5m]) / 1024^2) by (topic)
```
