# SketchBench Setup on Google Kubernetes Engine (GKE)

## GKE cluster configuration

- Location type: Zonal
- Zone: us-west1-a
- Control plane version
  - Release channel: Regular
  - Version: 1.19.9-gke.1900
- Node image type: Container-Optimized OS with Containerd

### Node pools

SketchBench utilizes 5 node pools:

|                             |                    Description                    |   Node type   | Number of Nodes | Disk Size per Node |
|:---------------------------:|:-------------------------------------------------:|:-------------:|:---------------:|:------------------:|
| **benchmark-control-plane** | SketchBench components such as the data generator | c2-standard-4 |        3        |        100GB       |
| **data-ingestion**          |  Isolated Apache Kafka cluster for data ingestion | c2-standard-4 |        3        |        100GB       |
| **system-under-test**       |          Isolated SUT (e.g. Apache Spark)         | c2-standard-4 |        3        |        100GB       |
| **observability**           |       Observability stack (e.g. Prometheus)       | c2-standard-4 |        3        |        100GB       |
| **data-plane**              |    Persistency services (e.g. HDFS & Zookeeper)   | c2-standard-4 |        3        |        100GB       |

#### Node pools during development

|                             |                    Description                    |   Node Type   | Number of Nodes | Disk Size per Node |
|:---------------------------:|:-------------------------------------------------:|:-------------:|:---------------:|:------------------:|
| **benchmark-control-plane** | SketchBench components such as the data generator | e2-standard-2 |        3        |        25GB        |
| **data-ingestion**          |  Isolated Apache Kafka cluster for data ingestion | e2-standard-2 |        2        |        25GB        |
| **system-under-test**       |          Isolated SUT (e.g. Apache Spark)         | c2-standard-4 |        5        |        25GB        |
| **observability**           |       Observability stack (e.g. Prometheus)       | e2-standard-2 |        3        |        25GB        |
| **data-plane**              |    Persistency services (e.g. HDFS & Zookeeper)   | e2-standard-2 |        2        |        25GB        |

## `gcloud` commands

### K8s cluster with default pool for benchmark control plane

```bash
gcloud beta container \
--project "sketchbench-320005" clusters create "sketchbench-dev-cluster" \
--zone "us-west1-a" \
--no-enable-basic-auth \
--cluster-version "1.19.9-gke.1900" \
--release-channel "regular" \
--machine-type "e2-highcpu-4" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "25" \
--node-labels sketchbench/pool=benchmark-control-plane \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--max-pods-per-node "110" \
--num-nodes "3" \
--no-enable-cloud-logging \
--enable-ip-alias \
--network "projects/sketchbench-320005/global/networks/default" \
--subnetwork "projects/sketchbench-320005/regions/us-west1/subnetworks/default" \
--no-enable-intra-node-visibility \
--default-max-pods-per-node "110" \
--enable-dataplane-v2 \
--no-enable-master-authorized-networks \
--addons HorizontalPodAutoscaling,HttpLoadBalancing,NodeLocalDNS,GcePersistentDiskCsiDriver \
--enable-autoupgrade \
--enable-autorepair \
--max-surge-upgrade 1 \
--max-unavailable-upgrade 0 \
--maintenance-window-start "2021-07-16T07:00:00Z" \
--maintenance-window-end "2021-07-16T23:00:00Z" \
--maintenance-window-recurrence "FREQ=WEEKLY;BYDAY=TU,TH" \
--enable-shielded-nodes \
--node-locations "us-west1-a"
```

### Node pool for data ingestion (Apache Kafka)

```bash
gcloud beta container \
--project "sketchbench-320005" node-pools create "data-ingestion" \
--cluster "sketchbench-dev-cluster" \
--zone "us-west1-a" \
--machine-type "e2-standard-2" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "25" \
--node-labels sketchbench/pool=data-ingestion \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--num-nodes "4" \
--enable-autoupgrade \
--enable-autorepair \
--max-surge-upgrade 1 \
--max-unavailable-upgrade 0 \
--max-pods-per-node "110" \
--node-locations "us-west1-a"
```

### Node pool for system under test (e.g. Apache Spark)

```bash
gcloud beta container \
--project "sketchbench-320005" node-pools create "system-under-test" \
--cluster "sketchbench-dev-cluster" \
--zone "us-west1-a" \
--machine-type "e2-highcpu-4" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "25" \
--node-labels sketchbench/pool=system-under-test \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--num-nodes "12" \
--enable-autoupgrade \
--enable-autorepair \
--max-surge-upgrade 1 \
--max-unavailable-upgrade 0 \
--max-pods-per-node "110" \
--node-locations "us-west1-a"
```

### Node pool for observability stack (e.g. Prometheus)

```bash
gcloud beta container \
--project "sketchbench-320005" node-pools create "observability" \
--cluster "sketchbench-dev-cluster" \
--zone "us-west1-a" \
--machine-type "e2-standard-4" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "25" \
--node-labels sketchbench/pool=observability \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--num-nodes "3" \
--enable-autoupgrade \
--enable-autorepair \
--max-surge-upgrade 1 \
--max-unavailable-upgrade 0 \
--max-pods-per-node "110" \
--node-locations "us-west1-a"
```

### Node pool for data plane (e.g. HDFS for Spark & Zookeeper for Kafka)

```bash
gcloud beta container \
--project "sketchbench-320005" node-pools create "data-plane" \
--cluster "sketchbench-dev-cluster" \
--zone "us-west1-a" \
--machine-type "e2-highcpu-4" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "25" \
--node-labels sketchbench/pool=data-plane \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--num-nodes "3" \
--enable-autoupgrade \
--enable-autorepair \
--max-surge-upgrade 1 \
--max-unavailable-upgrade 0 \
--max-pods-per-node "110" \
--node-locations "us-west1-a"
```

## Resources

### GKE Guide: Create a cost-optimized cluster

These changes get applied to a GKE cluster configuration when selecting "cost-optimized":

- Cluster name: cost-optimized-cluster-1
- Cluster zone: us-central1-c
- Cluster size: user-selected
- Machine type: auto-selected based on desired cluster size
- Cluster autoscaling: Enabled
- Autoscaling profile: Optimize utilization
- Vertical pod autoscaling: Enabled
- Node auto-provisioning: Enabled
- GKE usage metering: Enabled
