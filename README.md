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
--values \
./helm-values/values-gke-espbench-bullet.yaml \
sketchbench \
sketchbench/sketchbench \
--version 0.2.7
```

#### ESPBench data with Zeppelin for non-sketch-based queries

```bash
helm install \
--values ./helm-values/values-gke-espbench-standalone.yaml \
sketchbench \
sketchbench/sketchbench \
--version 0.2.7
```

---

### `sketchbench-data-ingestion-nexmark`

> Data Sender: [`sketchbench-data-ingestion-nexmark`](https://github.com/SketchBench/sketchbench-data-ingestion-nexmark)

#### NEXMark data with Bullet & Zeppelin for sketch-based queries

```bash
helm install \
--values \
./helm-values/values-gke-nexmark-bullet.yaml \
sketchbench \
sketchbench/sketchbench \
--version 0.2.7
```

#### NEXMark data with Zeppelin for non-sketch-based queries

```bash
helm install \
--values ./helm-values/values-gke-nexmark-standalone.yaml \
sketchbench \
sketchbench/sketchbench \
--version 0.2.7
```

---

### Accessing SketchBench components

#### Bullet

Two services are required to use the Bullet UI locally; open each in a separate terminal:

```bash
kubectl port-forward service/sketchbench-bullet-ui 8800:8800
kubectl port-forward service/sketchbench-bullet-web-service 9999:9999
```

#### Zeppelin

```bash
kubectl port-forward service/sketchbench-zeppelin 8080:8080
```

#### Grafana

```bash
kubectl port-forward service/sketchbench-grafana 8000:80
```

#### Bullet Spark UI

```bash
kubectl port-forward service/sketchbench-bullet-spark-backend-ui 4040:4040
```

#### Zeppelin Spark UI

```bash
kubectl port-forward service/sketchbench-zeppelin 4040:4040
```
