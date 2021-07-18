# SketchBench

> This repo contains instructions and scripts to run SketchBench.

## Deployment to Google Kubernetes Engine (GKE)

Check out [`gke.md`](https://github.com/SketchBench/sketchbench/blob/main/README.md) for setting up a GKE cluster with multiple node pools.

## Install SketchBench as Helm Chart

First, add the [SketchBench charts repo](https://github.com/SketchBench/charts) to Helm:

```bash
helm repo add sketchbench https://sketchbench.github.io/charts/
helm repo update
```

### `sketchbench-data-ingestion-espbench`

> Data Sender: [`sketchbench-data-ingestion-espbench`](https://github.com/SketchBench/sketchbench-data-ingestion-espbench)

#### ESPBench data with Bullet & Zeppelin for sketch-based queries

```bash
helm install --devel -f ./helm-values/values-gke-espbench-bullet.yaml my-sketchbench sketchbench/sketchbench
```

#### ESPBench data with Zeppelin for non-sketch-based queries

```bash
helm install --devel -f ./helm-values/values-gke-espbench-no-bullet.yaml my-sketchbench sketchbench/sketchbench
```

### `sketchbench-data-ingestion-tester`

> Data Generator: [`sketchbench-data-ingestion-tester`](https://github.com/SketchBench/sketchbench-data-ingestion-tester)

#### Test data with Bullet & Zeppelin for sketch-based queries

```bash
helm install --devel -f ./helm-values/values-gke-tester-bullet.yaml my-sketchbench sketchbench/sketchbench
```

#### Test data with Zeppelin for non-sketch-based queries

```bash
helm install --devel -f ./helm-values/values-gke-tester-no-bullet.yaml my-sketchbench sketchbench/sketchbench
```
