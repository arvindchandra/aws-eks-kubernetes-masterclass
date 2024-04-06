# EKS Storage -  Storage Classes, Persistent Volume Claims

## Step-01: Introduction
- We are going to create a MySQL Database with persistence storage using AWS EBS Volumes

| Kubernetes Object  | YAML File |
| ------------- | ------------- |
| Storage Class  | 01-storage-class.yml |
| Persistent Volume Claim | 02-persistent-volume-claim.yml   |
| Config Map  | 03-UserManagement-ConfigMap.yml  |
| Deployment, Environment Variables, Volumes, VolumeMounts  | 04-mysql-deployment.yml  |
| ClusterIP Service  | 05-mysql-clusterip-service.yml  |

POD WILL RESTART AND USE THE SAME DB

## Step-02: Create following Kubernetes manifests
### Create Storage Class manifest
- https://kubernetes.io/docs/concepts/storage/storage-classes/#volume-binding-mode
- **Important Note:** `WaitForFirstConsumer` mode will delay the volume binding and provisioning  of a PersistentVolume until a Pod using the PersistentVolumeClaim is created. 

### Create Persistent Volume Claims manifest
```
# Create Storage Class & PVC
kubectl apply -f kube-manifests/

# List Storage Classes
kubectl get sc

# List PVC
kubectl get pvc 

# List PV
kubectl get pv
```
### Create ConfigMap manifest
- We are going to create a `usermgmt` database schema during the mysql pod creation time which we will leverage when we deploy User Management Microservice. 

### Create MySQL Deployment manifest
- Environment Variables
- Volumes
- Volume Mounts

### Create MySQL ClusterIP Service manifest
- At any point of time we are going to have only one mysql pod in this design so `ClusterIP: None` will use the `Pod IP Address` instead of creating or allocating a separate IP for `MySQL Cluster IP service`.   

## Step-03: Create MySQL Database with all above manifests
```
# Create MySQL Database
kubectl apply -f kube-manifests/

# List PVC
kubectl get pvc 

# List PV
kubectl get pv

# List pods
kubectl get pods 

```
Ensure that you are doing below things
1. Create an empty volume with sufficient space and update pv.yaml with it. Also ensure if the zone is different from us-east-1a then update that.
2. Ensure that one node is running in the zone where volume is created. Also update that in  mysql-deployment.yml

## Step-04: Connect to MySQL Database
```
First time work:
Login to pod:
kubectl exec pod/mysql-<pod suffix randomly created> -it -- /bin/bash 

Login to mysql in the pod shell
mysql -p
(Give the password from config file)

Create DB
CREATE DATABASE menagerie;
Change to DB
USE menagerie
Create a table
CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20),
       species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
verify it is created
SHOW TABLES;
