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
--devel \
--values ./helm-values/values-gke-espbench-bullet.yaml \
my-sketchbench \
sketchbench/sketchbench
```

#### ESPBench data with Zeppelin for non-sketch-based queries

```bash
helm install \
--devel \
--values ./helm-values/values-gke-espbench-no-bullet.yaml \
my-sketchbench \
sketchbench/sketchbench
```

---

### `sketchbench-data-ingestion-tester`

> Data Generator: [`sketchbench-data-ingestion-tester`](https://github.com/SketchBench/sketchbench-data-ingestion-tester)

#### Test data with Bullet & Zeppelin for sketch-based queries

```bash
helm install \
--devel \
--values ./helm-values/values-gke-tester-bullet.yaml \
my-sketchbench \
sketchbench/sketchbench
```

#### Test data with Zeppelin for non-sketch-based queries

```bash
helm install \
--devel \
--values ./helm-values/values-gke-tester-no-bullet.yaml \
my-sketchbench \
sketchbench/sketchbench
```

## Development setup

### Install observability stack with `tobs`

```bash
helm repo add timescale https://charts.timescale.com/
helm repo update
helm install --devel --values ./helm-values/values-tobs.yaml sketchbench-o11y timescale/tobs
```

### Install SketchBench stack for ESPBench with Bullet, Zeppelin, and Spark

```bash
helm install \
--devel \
--values ./helm-values/values-gke-espbench-bullet-dev.yaml \
sketchbench-espbench \
sketchbench/sketchbench
```

### Install SketchBench stack for NEXMark with Bullet, Zeppelin, and Spark

```bash
helm install \
--devel \
--values \
./helm-values/values-gke-nexmark-bullet-dev.yaml \
sketchbench-nexmark \
sketchbench/sketchbench
```

### Install SketchBench "standalone" stack for ESPBench

```bash
helm install \
--devel \
--values \
./helm-values/values-gke-espbench-standalone-dev.yaml \
sketchbench-espbench-standalone \
sketchbench/sketchbench
```

### Install SketchBench "standalone" stack for NEXMark

```bash
helm install \
--devel --values \
./helm-values/values-gke-nexmark-standalone-dev.yaml \
sketchbench-nexmark-standalone \
sketchbench/sketchbench
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
--devel \
--values \
./helm-values/values-gke-zeppelin-standalone-dev.yaml \
sketchbench-zeppelin-standalone \
sketchbench/zeppelin
```
