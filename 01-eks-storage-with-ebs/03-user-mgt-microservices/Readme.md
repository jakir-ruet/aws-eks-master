## Install & Configuration of `User Management Microservice` with `MySQL database`

Here's a clean step-by-step guide for deploying your `User Management Microservice` with `MySQL database` on Kubernetes using the provided information.

### Step 01: Introduction
We'll deploy:

- A User Management Microservice
- A MySQL database

The microservice connects to the `usermgmt` schema and provides `REST API`s to:

- `Create`, `List`, `Update`, `Delete` Users
- `Health check`

#### User Management Microservice Credential of MySQL

## Environment Variables

| Key Name    | Value           |
| ----------- | --------------- |
| DB_HOSTNAME | mysql           |
| DB_PORT     | 3306            |
| DB_NAME     | usermgmt        |
| DB_USERNAME | root            |
| DB_PASSWORD | mydbpass@054003 |

### Step 03: Deploy Microservice

```bash
kubectl apply -f kube-manifests/
kubectl get pods
kubectl logs -f <POD_NAME>
kubectl get sc,pvc,pv
```

📌 **Issue:**

If you apply all manifests simultaneously, the microservice may restart until MySQL is ready. In the next section, consider using an initContainer to wait for MySQL.

### Step 03: Access Application & API

```bash
kubectl get svc
kubectl get nodes -o wide
http://<WORKER_NODE_PUBLIC_IP>:31231/usermgmt/health-status
```

### Step 04: Test APIs via Postman

- File: `AWS-EKS-Masterclass-Microservices.postman_collection.json`
- Create Environment
  - Environment Name: `UMS-NodePort`
  - Variable: `url`
  	- Value: `http://<WORKER_NODE_PUBLIC_IP>:31231`

**Test APIs - CRUD**

- Health Status

```bash
GET {{url}}/usermgmt/health-status
```

- Create User

```bash
POST {{url}}/usermgmt/user
Body:
{
    "username": "admin1",
    "email": "jttfbd@gmail.com",
    "role": "ROLE_ADMIN",
    "enabled": true,
    "firstname": "fname1",
    "lastname": "lname1",
    "password": "Pass@123"
}
```

- List Users

```bash
GET {{url}}/usermgmt/users
```

- Update User

```bash
PUT {{url}}/usermgmt/user
Body:
{
    "username": "admin1",
    "email": "jttfbd@gmail.com",
    "role": "ROLE_ADMIN",
    "enabled": true,
    "firstname": "fname2",
    "lastname": "lname2",
    "password": "Pass@123"
}
```

- Delete User

```bash
DELETE {{url}}/usermgmt/user/admin1
```

### Step 05: Verify in MySQL DB

```bash
kubectl run -it --rm --image=mysql:8 --restart=Never mysql-client -- mysql -h mysql -u root -mydbpass@054003
```

```bash
show databases;
use usermgmt;
show tables;
select * from users;
```

### Step 06: Clean-Up

```bash
kubectl delete -f kube-manifests/
kubectl get pods
kubectl get sc,pvc,pv
```

### Let's enhance (Optional)

Our `User Management Microservice Deployment` with an `initContainer` that waits for the `MySQL database` to become ready before the main container starts.

#### Why Use `initContainers`?
The microservice may start before MySQL is ready, causing it to fail and restart. An `initContainer` solves this by `blocking the main container` until the DB is reachable.

#### Updated Deployment YAML with `initContainer` - 06-user-mgt-microservice-dep-service.yaml (Enhanced)

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: usermgmt-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: usermgmt
  template:
    metadata:
      labels:
        app: usermgmt
    spec:
      initContainers:
      - name: wait-for-mysql
        image: busybox:1.28
        command: ['sh', '-c', 'until nc -z mysql 3306; do echo waiting for mysql; sleep 2; done;']
      containers:
      - name: usermgmt
        image: your-dockerhub-username/usermgmt-microservice:latest
        ports:
        - containerPort: 8080
        env:
        - name: DB_HOSTNAME
          value: "mysql"
        - name: DB_PORT
          value: "3306"
        - name: DB_NAME
          value: "usermgmt"
        - name: DB_USERNAME
          value: "root"
        - name: DB_PASSWORD
          value: "mydbpass@054003"
```

**Explanation:**

- `initContainer` uses a lightweight busybox image.
- It runs a `while` loop that waits for MySQL on port 3306 to be accessible.
- Only after MySQL is available, it starts the `main container`.

**Reapply the deployment**

```bash
kubectl apply -f kube-manifests/06-user-mgt-microservice-dep-service.yml
```

#### Logging the Wait (Optional)

```bash
kubectl describe pod <usermgmt-pod-name>
```

- Should see

```bash
waiting for mysql
waiting for mysql
```
