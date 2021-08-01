# SketchBench Setup on Google Kubernetes Engine (GKE)

## GKE cluster configuration

- Location type: Zonal
- Zone: us-west1-a
- Control plane version
  - Release channel: Regular
  - Version: 1.20.8-gke.900
- Node image type: Container-Optimized OS with Containerd

### Node pools

SketchBench utilizes 5 node pools:

|                             |                    Description                    |   Node type   | Number of Nodes | Disk Size per Node |
|-----------------------------|---------------------------------------------------|---------------|-----------------|--------------------|
| **default-pool**            | K8s and SketchBench control plane components      | n2-standard-4 |        3        |        100GB       |
| **data-generation**         | SketchBench data sender (ESPBench/NEXMark)        | n2-standard-4 |        1        |        100GB       |
| **data-ingestion**          |  Isolated Apache Kafka cluster for data ingestion | c2-standard-8 |        1        |        100GB       |
| **system-under-test**       |          Isolated SUT (e.g. Apache Spark)         | c2-standard-8 |        5        |        100GB       |
| **observability**           |       Observability stack (e.g. Prometheus)       | n2-standard-4 |        3        |        100GB       |
| **data-plane**              |    Persistency services (e.g. HDFS & Zookeeper)   | n2-standard-4 |        3        |        100GB       |

## `gcloud` commands

### K8s cluster with default pool for benchmark control plane

```bash
gcloud beta container \
--project "cosmic-ascent-321403" clusters create "sketchbench-cluster" \
--zone "us-west1-a" \
--no-enable-basic-auth \
--cluster-version "1.20.8-gke.900" \
--release-channel "regular" \
--machine-type "n2-standard-4" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "100" \
--node-labels sketchbench/pool=control-plane \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--max-pods-per-node "110" \
--num-nodes "3" \
--no-enable-cloud-logging \
--enable-ip-alias \
--network "projects/cosmic-ascent-321403/global/networks/default" \
--subnetwork "projects/cosmic-ascent-321403/regions/us-west1/subnetworks/default" \
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

### Node pool for data-generation (ESPBench data set / NEXMark data generator)

```bash
gcloud beta container \
--project "cosmic-ascent-321403" node-pools create "data-generation" \
--cluster "sketchbench-cluster" \
--zone "us-west1-a" \
--machine-type "n2-standard-4" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "100" \
--node-labels sketchbench/pool=data-generation \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--num-nodes "1" \
--enable-autoupgrade \
--enable-autorepair \
--max-surge-upgrade 1 \
--max-unavailable-upgrade 0 \
--max-pods-per-node "110" \
--node-locations "us-west1-a"
```

### Node pool for data ingestion (Apache Kafka)

```bash
gcloud beta container \
--project "cosmic-ascent-321403" node-pools create "data-ingestion" \
--cluster "sketchbench-cluster" \
--zone "us-west1-a" \
--machine-type "c2-standard-8" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "100" \
--node-labels sketchbench/pool=data-ingestion \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--num-nodes "1" \
--enable-autoupgrade \
--enable-autorepair \
--max-surge-upgrade 1 \
--max-unavailable-upgrade 0 \
--max-pods-per-node "110" \
--node-locations "us-west1-a"
```

### Node pool for system under test (Apache Spark)

```bash
gcloud beta container \
--project "cosmic-ascent-321403" node-pools create "system-under-test" \
--cluster "sketchbench-cluster" \
--zone "us-west1-a" \
--machine-type "c2-standard-8" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "100" \
--node-labels sketchbench/pool=system-under-test \
--metadata disable-legacy-endpoints=true \
--scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" \
--num-nodes "5" \
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
--project "cosmic-ascent-321403" node-pools create "observability" \
--cluster "sketchbench-cluster" \
--zone "us-west1-a" \
--machine-type "n2-standard-4" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "100" \
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
--project "cosmic-ascent-321403" node-pools create "data-plane" \
--cluster "sketchbench-cluster" \
--zone "us-west1-a" \
--machine-type "n2-standard-4" \
--image-type "COS_CONTAINERD" \
--disk-type "pd-ssd" \
--disk-size "100" \
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
