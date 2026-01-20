# README

## Prerequisites (one-time on the machine)

```bash
brew install kubectl kind
```


## Install Docker Desktop, open it, wait until it says “Docker is running”.

```bash
docker ps
```

## Clone the repo

```bash
git clone git@github.com:siddmkrj/db-cluster.git
cd db-cluster
```

## Create local persistent storage folders (CRITICAL)

```bash
mkdir -p ~/k8s-data/mysql
mkdir -p ~/k8s-data/redis
mkdir -p ~/k8s-data/neo4j
mkdir -p ~/k8s-data/weaviate
```

## Create the Kubernetes cluster

```bash
kind delete cluster --name db-cluster
kind create cluster --name db-cluster --config kind/kind-db.yml
```

## Verify disk mount (IMPORTANT)

```bash
docker exec -it db-cluster-control-plane ls /k8s-data
```

## Create namespaces

```bash
kubectl apply -f namespaces/
kubectl get namespaces
```

## Create Persistent Volumes & Claims

```bash
kubectl apply -f storage/local-storage-class.yaml
kubectl get storageclass

kubectl apply -f storage/redis/
kubectl apply -f storage/mysql/
kubectl apply -f storage/neo4j/
kubectl apply -f storage/weaviate/

kubectl get pv
kubectl get pvc -A
```

## Create secrets

```bash
mkdir -p secrets
kubectl create secret generic mysql-secret \
  --from-literal=MYSQL_ROOT_PASSWORD=devpassword \
  -n database

kubectl create secret generic neo4j-secret \
  --from-literal=NEO4J_AUTH=neo4j/Ognam321! \
  -n database

kubectl get secrets -n database
```

## Deploy databases

```bash
kubectl apply -f databases/redis/
kubectl apply -f databases/mysql/
kubectl apply -f databases/neo4j/
kubectl apply -f databases/weaviate/

kubectl get pods -A
kubectl get services -A
```


## Port Forward

```bash
kubectl port-forward -n database svc/mysql 3306:3306 >/tmp/mysql-portforward.log 2>&1 & \
kubectl port-forward -n database svc/redis 6379:6379 >/tmp/redis-portforward.log 2>&1 & \
kubectl port-forward -n database svc/weaviate 8080:8080 >/tmp/weaviate-portforward.log 2>&1 & \
kubectl port-forward -n database svc/neo4j 7474:7474 >/tmp/neo4j-portforward.log 2>&1 &
```


## Cleanup all

```bash
kubectl delete ns database --ignore-not-found
kubectl delete all --all --all-namespaces
kubectl delete pvc --all --all-namespaces
kubectl delete pv --all
kubectl delete configmap --all --all-namespaces
kubectl delete secret --all --all-namespaces
kind delete cluster --name db-cluster
```

