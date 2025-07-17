## Kubernetes Secrets - Quick Recap and Tips
### Step 1: What are Secrets?
- Secrets store sensitive info (passwords, tokens, keys).
- Safer than embedding secrets in Pod specs or container images.

### Step 2: Create a Secret for MySQL Password

#### Encode your password in base64:

```bash
echo -n 'mydbpass@054003' | base64 # output bXlkYnBhc3NAMDU0MDAz
```

#### Secret manifest

```bash
apiVersion: v1
kind: Secret
metadata:
  name: mysql-db-password
type: Opaque
data:
  db-password: bXlkYnBhc3NAMDU0MDAz
```
**Note:** The `type: Opaque` means Kubernetes treats the secret data as arbitrary key-value pairs.

### Step 3: Inject Secret into Deployments

```bash
env:
  - name: MYSQL_ROOT_PASSWORD
    valueFrom:
      secretKeyRef:
        name: mysql-db-password
        key: db-password
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: mysql-db-password
        key: db-password
```

### Step 4: Deploy & Test

```bash
kubectl apply -f kube-manifests/
kubectl get pods
```

### Step 5: Clean Up

```bash
kubectl delete -f kube-manifests/
kubectl get pods
kubectl get sc,pvc,pv
```
