## Install & Configuration - EKS Storage, Storage Classes, Persistent Volume Claims

Here's a complete walkthrough to set up a `MySQL database` with `persistent storage` on `AWS EKS` using `EBS volumes`, broken into the steps you've outlined. This solution follows best practices with AWS EBS and Kubernetes on EKS.

### Step-01: Workflow Plan

We will create the following Kubernetes objects

| Kubernetes Object         | YAML File                         |
| ------------------------- | --------------------------------- |
| StorageClass              | `01-storage-class.yaml`           |
| PersistentVolumeClaim     | `02-persistent-volume-claim.yaml` |
| ConfigMap (schema init)   | `03-user-mgt-configmap.yaml`      |
| Deployment (MySQL)        | `04-mysql-deployment.yaml`        |
| ClusterIP Service (MySQL) | `05-mysql-clusterip-service.yaml` |

### Step-02: Deploy Everything

```bash
kubectl apply -f kube-manifests/
kubectl get sc
kubectl get pvc
kubectl get pv
kubectl get pods
kubectl get pods -l app=mysql
```

### Step-03: Connect to MySQL

```bash
kubectl run -it --rm --image=mysql:8 --restart=Never mysql-client -- \
  mysql -h mysql -mydbpass@054003
```

- Or

```bash
kubectl run -it --rm --image=mysql:latest --restart=Never mysql-client -- \
  mysql -h mysql -mydbpass@054003
```

#### Show databases

```bash
SHOW DATABASES;
```
