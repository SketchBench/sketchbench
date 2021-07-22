# SketchBench

> This repo contains instructions and scripts to run SketchBench.

## Deployment to Google Kubernetes Engine (GKE)

Check out [`gke.md`](https://github.com/SketchBench/sketchbench/blob/main/gke.md) for setting up a GKE cluster with multiple node pools.

## Install SketchBench as Helm Chart

First, add the [SketchBench charts repo](https://github.com/SketchBench/charts) to Helm:

```bash
helm repo add sketchbench https://sketchbench.github.io/charts/
helm repo update
```

---

### `sketchbench-data-ingestion-espbench`

> Data Sender: [`sketchbench-data-ingestion-espbench`](https://github.com/SketchBench/sketchbench-data-ingestion-espbench)

#### ESPBench data with Bullet & Zeppelin for sketch-based queries

```bash
helm install \
--values ./helm-values/values-gke-espbench-bullet.yaml \
my-sketchbench \
sketchbench/sketchbench \
--version 0.2.1
```

#### ESPBench data with Zeppelin for non-sketch-based queries

```bash
helm install \
--values ./helm-values/values-gke-espbench-no-bullet.yaml \
my-sketchbench \
sketchbench/sketchbench \
--version 0.2.1
```

---

### `sketchbench-data-ingestion-tester`

> Data Generator: [`sketchbench-data-ingestion-tester`](https://github.com/SketchBench/sketchbench-data-ingestion-tester)

#### Test data with Bullet & Zeppelin for sketch-based queries

```bash
helm install \
--values ./helm-values/values-gke-tester-bullet.yaml \
my-sketchbench \
sketchbench/sketchbench \
--version 0.2.1
```

#### Test data with Zeppelin for non-sketch-based queries

```bash
helm install \
--values ./helm-values/values-gke-tester-no-bullet.yaml \
my-sketchbench \
sketchbench/sketchbench \
--version 0.2.1
```

## Development setup

### Install SketchBench stack for ESPBench with Bullet, Zeppelin, and Spark

```bash
helm install \
--values ./helm-values/values-gke-espbench-bullet-dev.yaml \
sketchbench-espbench \
sketchbench/sketchbench \
--version 0.2.1
```

### Install SketchBench stack for NEXMark with Bullet, Zeppelin, and Spark

```bash
helm install \
--values \
./helm-values/values-gke-nexmark-bullet-dev.yaml \
sketchbench-nexmark \
sketchbench/sketchbench \
--version 0.2.1
```

### Install SketchBench "standalone" stack for ESPBench

```bash
helm install \
--values \
./helm-values/values-gke-espbench-standalone-dev.yaml \
sketchbench-espbench-standalone \
sketchbench/sketchbench \
--version 0.2.1
```

### Install SketchBench "standalone" stack for NEXMark

```bash
helm install \
--values \
./helm-values/values-gke-nexmark-standalone-dev.yaml \
sketchbench-nexmark-standalone \
sketchbench/sketchbench \
--version 0.2.1
```

### Install Spark cluster for "standalone" setup

```bash
helm install \
--values \
./helm-values/values-gke-spark-standalone-dev.yaml \
sketchbench-spark-standalone \
bitnami/spark \
--version 5.6.2
```

### Install Zeppelin for "standalone" setup

```bash
helm install \
--values \
./helm-values/values-gke-zeppelin-standalone-dev.yaml \
sketchbench-zeppelin-standalone \
sketchbench/zeppelin \
--version 0.9.0
```

### Accessing Bullet locally

#### Bullet for ESPBench

Two services are required to use the Bullet UI locally; open each in a separate terminal:

```bash
kubectl port-forward service/sketchbench-espbench-bullet-ui 8800:8800
kubectl port-forward service/sketchbench-espbench-bullet-web-service 9999:9999
```

#### Bullet for NEXMark

Two services are required to use the Bullet UI locally; open each in a separate terminal:

```bash
kubectl port-forward service/sketchbench-nexmark-bullet-ui 8800:8800
kubectl port-forward service/sketchbench-nexmark-bullet-web-service 9999:9999
```

### Accessing Zeppelin locally

#### Zeppelin for ESPBench (Bullet)

```bash
kubectl port-forward service/sketchbench-espbench-zeppelin 8080:8080
```

#### Zeppelin for NEXMark (Bullet)

```bash
kubectl port-forward service/sketchbench-nexmark-zeppelin 8080:8080
```

#### Zeppelin for "standalone"

```bash
kubectl port-forward service/sketchbench-zeppelin-standalone 8080:8080
```

When starting a Spark job through Zeppelin, open Zeppelin's Spark UI with:

```bash
kubectl port-forward service/sketchbench-zeppelin-standalone 4040:4040
```

### Accessing Grafana locally

```bash
kubectl port-forward service/sketchbench-o11y-grafana 8000:80
```
