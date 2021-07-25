# Metrics from TimescaleDB

## Spark

### Workers / Executors

#### Workers CPU Usage in Percent

```sql
SELECT TIME,
  VALUE AS CPU_USAGE,
  VAL(POD_ID) AS POD
FROM PROM_METRIC."node_namespace_pod_container:container_cpu_usage_seconds__1578"
WHERE VAL(POD_ID) LIKE '%spark-worker%'
  AND CONTAINER_ID > 0
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

#### Workers Memory in Bytes

```sql
SELECT TIME, VALUE AS VALUE_MEM_BYTES,
  VAL(POD_ID) AS POD
FROM PROM_METRIC.CONTAINER_MEMORY_WORKING_SET_BYTES
WHERE VAL(POD_ID) LIKE '%spark-worker%'
  AND CONTAINER_ID > 0
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

#### RDD Blocks

```sql
SELECT TIME,
  VALUE AS RDD_BLOCKS,
  VAL(EXECUTOR_ID_ID) AS EXECUTOR
FROM PROM_METRIC."metrics_executor_rddBlocks"
WHERE VAL(EXECUTOR_ID_ID) <> 'driver'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

#### Completed Tasks

```sql
SELECT TIME,
  VALUE AS COMPLETED_TASKS,
  VAL(EXECUTOR_ID_ID) AS EXECUTOR
FROM PROM_METRIC."metrics_executor_completedTasks_total"
WHERE VAL(EXECUTOR_ID_ID) <> 'driver'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

#### Active Tasks

```sql
SELECT TIME,
  VALUE AS ACTIVE_TASKS,
  VAL(EXECUTOR_ID_ID) AS EXECUTOR
FROM PROM_METRIC."metrics_executor_activeTasks"
WHERE VAL(EXECUTOR_ID_ID) <> 'driver'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

### Driver

#### Driver CPU Usage in Percent

```sql
SELECT TIME,
  VALUE AS CPU_USAGE,
  VAL(POD_ID) AS POD
FROM PROM_METRIC."node_namespace_pod_container:container_cpu_usage_seconds__1578"
WHERE VAL(POD_ID) LIKE '%spark-backend%'
  AND CONTAINER_ID > 0
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

#### Driver Memory in MB

```sql
SELECT TIME, VALUE AS VALUE_MEM_BYTES,
  VAL(POD_ID) AS POD
FROM PROM_METRIC.CONTAINER_MEMORY_WORKING_SET_BYTES
WHERE VAL(POD_ID) LIKE '%spark-backend%'
  AND CONTAINER_ID > 0
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

## Kafka

### Network

#### Bytes In per sec (One Minute Rate)

```sql
SELECT TIME, VALUE AS BYTESINPERSEC_ONEMINUTERATE
FROM PROM_METRIC.KAFKA_SERVER_BROKERTOPICMETRICS_BYTESINPERSEC_ONEMINUTERATE
WHERE VAL(TOPIC_ID) = 'SketchBench-1-1'
  AND VAL(JOB_ID) = 'sketchbench-espbench-kafka-jmx-metrics'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

#### Bytes Out per sec (One Minute Rate)

```sql
SELECT TIME, VALUE AS BYTESOUTPERSEC_ONEMINUTERATE
FROM PROM_METRIC.KAFKA_SERVER_BROKERTOPICMETRICS_BYTESOUTPERSEC_ONEMINUTERATE
WHERE VAL(TOPIC_ID) = 'SketchBench-1-1'
  AND VAL(JOB_ID) = 'sketchbench-espbench-kafka-jmx-metrics'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

### Requests

#### Requests In per /sec (One Minute Rate)

```sql
SELECT TIME,
  VALUE AS REQUESTS_PER_SEC_IN
FROM PROM_METRIC.KAFKA_SERVER_BROKERTOPICMETRICS_TOTALPRODUCEREQUESTSPERSE_1562
WHERE VAL(TOPIC_ID) = 'SketchBench-1-1'
  AND VAL(JOB_ID) = 'sketchbench-espbench-kafka-jmx-metrics'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

#### Requests Out per sec (One Minute Rate)

```sql
SELECT TIME,
  VALUE AS REQUESTS_PER_SEC_OUT
FROM PROM_METRIC.KAFKA_SERVER_BROKERTOPICMETRICS_TOTALFETCHREQUESTSPERSEC__1572
WHERE VAL(TOPIC_ID) = 'SketchBench-1-1'
  AND VAL(JOB_ID) = 'sketchbench-espbench-kafka-jmx-metrics'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

### Lag

#### Lag by Consumer Group

```sql
SELECT TIME,
  VALUE AS CONSUMERGROUP_LAG,
  VAL(CONSUMERGROUP_ID) AS CONSUMERGROUP
FROM PROM_METRIC.KAFKA_CONSUMERGROUP_LAG_SUM
WHERE VAL(TOPIC_ID) = 'SketchBench-1-1'
  AND VAL(JOB_ID) = 'sketchbench-espbench-kafka-metrics'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

### Offset

#### Current Consumer Offset by Consumer Group

```sql
SELECT TIME,
  VALUE AS CURRENT_CONSUMER_OFFSET,
  VAL(CONSUMERGROUP_ID) AS CONSUMERGROUP
FROM PROM_METRIC.KAFKA_CONSUMERGROUP_CURRENT_OFFSET
WHERE VAL(TOPIC_ID) = 'SketchBench-1-1'
  AND VAL(JOB_ID) = 'sketchbench-espbench-kafka-metrics'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```

#### Current Producer Offset by Consumer Group

```sql
SELECT TIME,
  VALUE AS CURRENT_PRODUCER_OFFSET
FROM PROM_METRIC.KAFKA_TOPIC_PARTITION_CURRENT_OFFSET
WHERE VAL(TOPIC_ID) = 'SketchBench-1-1'
  AND VAL(JOB_ID) = 'sketchbench-espbench-kafka-metrics'
  AND TIME > NOW() - INTERVAL '10 hour'
ORDER BY TIME ASC
```
