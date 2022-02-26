# efk-stack
# Summary
This repository hosts examples and instruction on how to simply configure efk stack on local kubernetes cluster.

---
**Warning**

The installation and configuration manifests are for demo purposes and __are not__ suited for production environments. 

---

# Overview
we will follow below steps for this demo :  

- Setup local kubernetes cluster using docker desktop and [KIND](https://github.com/kubernetes-sigs/kind/releases)

- install elastic search using helm 
- Install and config [fluentd](https://www.fluentd.org/).  



# Setup Cluster
Please first clone the repository on your local machine and change directory to the root of the this git repository.

We will use [KIND](https://github.com/kubernetes-sigs/kind/releases) to create a control-plane node with two worker nodes.

```bash
# In case any clusters already exists from previous atempts. run below:
# kind delete cluster --name my-cluster

kind create cluster --name my-cluster --image kindest/node:v1.22.0 --config demo-cluster.yaml
```
## Add helm repo
1. Run the command to add helm repo:

   ```bash
   helm repo add stable https://charts.helm.sh/stable
   ```

2. install elastic search:

   ```bash
    helm install elasticsearch stable/elasticsearch
   ```
    - verify installation and access to service

     ```bash
    kubectl port-forward svc/elasticsearch-client 9200
     ```
    ```bash
    http://localhost:9200/
   ```
    - verify elastic search indexes

     ```bash
    http://localhost:9200/_cat/indices?v
     ```

3. install fluentd as daemonset:
    - first we will create nessary Cluster role,serviceaccount and rolebinding objects

   ```bash
    kubectl apply -f role_creation.yaml
   ```

      ```bash
    kubectl apply -f fluentd-daemonset-elasticsearch.yaml
   ```

 4. install kibana:

   ```bash
    helm install kibana stable/kibana -f kibana-values.yaml
   ```  
- verify installation and access to service

    ```bash
    kubectl port-forward svc/kibana 444:443
     ```
 5. Run Simple application to generate dummy logs

   ```bash
 kubectl apply -f counter.yaml
   ```  