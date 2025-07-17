## Welcome to EKS Storage with EBS

The MySQL database will leverage Amazon EBS volumes for persistent storage, provisioned via the EBS CSI Driver.

### Topics
1. Install EBS CSI Driver
   - Set up the Amazon EBS Container Storage Interface (CSI) Driver on the K8s cluster to enable dynamic provisioning of EBS volumes.
2. Create MySQL Database Deployment & ClusterIP Service
   - Deploy a MySQL database StatefulSet or Deployment using persistent EBS volume.
   - Expose the MySQL service internally using a ClusterIP service.
3. Create User Management Microservice Deployment & NodePort Service
   - Deploy the User Management microservice as a K8s Deployment.
   - Expose it externally using a NodePort service for direct access.

### Workflow Plan

| **Kubernetes Object**                                  | **YAML File**                     |
| ------------------------------------------------------ | --------------------------------- |
| **Storage Class**                                      | `01-storage-class.yaml`           |
| **Persistent Volume Claim (PVC)**                      | `02-persistent-volume-claim.yaml` |
| **ConfigMap** (User Management Microservice settings)  | `03-user-mgt-configmap.yaml`      |
| **MySQL Deployment**                                   |
| Includes: Environment Variables, Volumes, VolumeMounts | `04-mysql-deployment.yaml`        |
| **MySQL ClusterIP Service**                            | `05-mysql-clusterip-service.yaml` |
| **User Management Microservice Deployment**            |
| Includes: Environment Variables                        | `06-user-mgt-deployment.yaml`     |
| **User Management NodePort Service**                   | `07-user-mgt-service.yaml`        |

### References

1. [Dynamic Volume Provisioning](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)
2. [AWS EBS CSI Driver](https://github.com/kubernetes-sigs/aws-ebs-csi-driver/tree/master/deploy/kubernetes/overlays/stable)
3. [K8s volume storage with EBS](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)
4. [AWS EBS CSI driver](https://github.com/kubernetes-sigs/aws-ebs-csi-driver)
5. [Dynamic Volume Provisioning](https://github.com/kubernetes-sigs/aws-ebs-csi-driver/tree/master/examples/kubernetes/dynamic-provisioning)
