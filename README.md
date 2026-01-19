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
kubectl apply -f storage/redis/
kubectl apply -f storage/mysql/
kubectl apply -f storage/neo4j/
kubectl apply -f storage/weaviate/
```
