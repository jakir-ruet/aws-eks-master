## EKS Storage with EBS (Step-by-Step)

### Step 1: Create IAM Policy for EBS CSI Driver (Go to IAM > Policies > Create Policy)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:CreateSnapshot",
        "ec2:CreateTags",
        "ec2:CreateVolume",
        "ec2:DeleteSnapshot",
        "ec2:DeleteTags",
        "ec2:DeleteVolume",
        "ec2:DescribeInstances",
        "ec2:DescribeSnapshots",
        "ec2:DescribeTags",
        "ec2:DescribeVolumes",
        "ec2:DetachVolume"
      ],
      "Resource": "*"
    }
  ]
}
```

- Name: `Amazon_EBS_CSI_Driver`
- Description: `Policy for EBS CSI driver to manage volumes`

### Step 2: Attach IAM Policy to Worker Node Role

- Get the node IAM role from your cluster

```bash
kubectl -n kube-system describe configmap aws-auth
```

- Look for the `rolearn` line, something like

```bash
rolearn: arn:aws:iam::<ACCOUNT_ID>:role/eksctl-<cluster-name>-nodegroup-NodeInstanceRole-XXXXX
```

- Go to `IAM > Roles`, find the above IAM role
  - Click the role
  - Go to `Permissions` tab
  - Click `Attach policies`
  - Search for `Amazon_EBS_CSI_Driver`
  - Click `Attach`

### Step 3: Install the EBS CSI Driver

```bash
kubectl version --client --short
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"
```

- This will install the necessary components under `kube-system` namespace:
  - `ebs-csi-controller`
  - `ebs-csi-node`

### Step 4: Verify the Driver Is Running

```bash
kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver
```

### ⚠️ Note on Best Practice
This method works well for learning environments and small clusters, but for `production`, AWS recommends using `IAM Roles for Service Accounts (IRSA)` instead of attaching policies directly to node roles. IRSA offers more granular and secure access control.
