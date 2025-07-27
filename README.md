# ray-train-samples
# Distributed Training with Ray

This repository is designed for running **multi-node distributed training jobs using [Ray](https://www.ray.io/)**. All examples and scripts are intended to work **within an AWS environment**, typically using **Amazon EKS** (Elastic Kubernetes Service) for orchestration and GPU-based EC2 instances for compute. At some point in the future, I'll extend the repo to GCP and Azure. 

> ⚠️ **Note**: This repository **does not manage infrastructure setup** (e.g., creating EKS clusters, setting up node groups, IAM roles, etc.). However, each training example will indicate the specific infrastructure configuration used—typically leveraging **NVIDIA T4 GPUs** for cost efficiency, though this may vary by workload.

---

## 🧪 Training Scripts

Training scripts in this repo support both:

- **MLflow-integrated runs**: For experiment tracking, logging parameters, metrics, and artifacts.
- **Standalone (non-MLflow) versions**: For simpler, lightweight runs or environments where MLflow is not needed.

---

## 📄 Example: RayCluster YAML

Below is a sample `RayCluster` YAML definition for deploying a Ray cluster in Kubernetes:

```yaml
apiVersion: ray.io/v1
kind: RayCluster
metadata:
  name: example-ray-cluster
spec:
  rayVersion: '2.9.0'
  headGroupSpec:
    serviceType: ClusterIP
    replicas: 1
    rayStartParams:
      dashboard-host: '0.0.0.0'
    template:
      spec:
        containers:
          - name: ray-head
            image: rayproject/ray:2.9.0
            resources:
              limits:
                nvidia.com/gpu: 1
                cpu: "4"
                memory: "16Gi"
  workerGroupSpecs:
    - groupName: worker-group
      replicas: 2
      rayStartParams: {}
      template:
        spec:
          containers:
            - name: ray-worker
              image: rayproject/ray:2.9.0
              resources:
                limits:
                  nvidia.com/gpu: 1
                  cpu: "4"
                  memory: "16Gi"

