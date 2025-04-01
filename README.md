# Hadoop Kubernetes Cluster in Kind (Developer Environment)

This project provides a local development environment for Hadoop running on Kubernetes using Kind (Kubernetes in Docker). It includes configurations for a basic Hadoop cluster with HDFS and YARN, plus optional integrations with Spark, Elasticsearch, and Trino for a complete data processing stack.

## Features

- **Local Hadoop cluster** with HDFS and YARN
- **Persistent storage** configuration
- **Optional integrations** with:
  - Spark for distributed processing
  - Elasticsearch for search capabilities
  - Trino (formerly PrestoSQL) for interactive queries
- **Developer-friendly** setup using Kind for local Kubernetes

## Prerequisites

- Docker installed
- Kind installed (`brew install kind` or see [installation docs](https://kind.sigs.k8s.io/docs/user/quick-start/#installation))
- kubectl installed
- At least 8GB RAM available for Docker

## Quick Start

1. **Create the Kind cluster**:
   ```bash
   kind create cluster --config kind-config.yaml
   ```

2. **Set up Hadoop**:
   ```bash
   kubectl apply -f hadoop-config.yaml
   kubectl apply -f hadoop-pv.yaml
   kubectl apply -f namenode.yaml
   kubectl apply -f datanode.yaml
   kubectl apply -f yarn.yaml
   ```

3. **(Optional) Add processing components**:
   ```bash
   kubectl create deployment spark --image=bitnami/spark
   kubectl create deployment elasticsearch --image=bitnami/elasticsearch
   kubectl create deployment trino --image=trinodb/trino
   ```

## Cluster Management

### Verify cluster status
```bash
kubectl get pods
```

### Initialize HDFS
```bash
kubectl exec -it deployment/namenode -- hdfs namenode -format
```

### Start Hadoop services
```bash
kubectl exec -it deployment/namenode -- start-dfs.sh
kubectl exec -it deployment/resourcemanager -- start-yarn.sh
```

## Configuration Files

- `kind-config.yaml`: Kind cluster configuration
- `hadoop-config.yaml`: Hadoop configuration maps
- `hadoop-pv.yaml`: Persistent volume setup for HDFS
- `namenode.yaml`: NameNode deployment
- `datanode.yaml`: DataNode deployment
- `yarn.yaml`: YARN ResourceManager deployment

## Architecture

```
+-----------------------+
|    Kubernetes Cluster |
|  (Kind)               |
|                       |
|  +-----------------+  |
|  | Hadoop Cluster  |  |
|  | - NameNode      |  |
|  | - DataNodes     |  |
|  | - YARN          |  |
|  +-----------------+  |
|                       |
|  Optional Components: |
|  - Spark             |
|  - Elasticsearch     |
|  - Trino             |
+-----------------------+
```

## Clean Up

To delete the cluster:
```bash
kind delete cluster
```

## Notes

- This setup is designed for development and testing purposes only
- For production use, consider proper security configurations and resource allocations
- The default configurations use minimal resources suitable for local development

## Troubleshooting

If pods fail to start:
1. Check resource usage: `docker stats`
2. Verify Kubernetes nodes: `kubectl get nodes`
3. Check pod logs: `kubectl logs <pod-name>`

For persistent volume issues, ensure your Docker has enough disk space allocated.