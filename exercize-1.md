# Kubernetes Basics Cheat Sheet


## Key Components of Kubernetes



 https://kubernetes.io/docs/concepts/architecture/


### Service type

For some parts of your application (for example, frontends) you may want to expose a Service onto an external IP address, one that's accessible from outside of your cluster.

Kubernetes Service types allow you to specify what kind of Service you want.

The available type values and their behaviors are:

1.  **ClusterIP**
    Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default that is used if you don't explicitly specify a type for a Service.You can expose the Service to the public internet using anIngress or aGateway.
2. **NodePort**
    Exposes the Service on each Node's IP at a static port (the NodePort).To make the node port available, Kubernetes sets up a cluster IP address,the same as if you had requested a Service of type: ClusterIP.
3. **LoadBalancer**
    Exposes the Service externally using an external load balancer. Kubernetes does not directly offer a load balancing component; you must provide one, or you can integrate your Kubernetes cluster with a cloud provider.
4. **ExternalName**
    Maps the Service to the contents of the externalName field (for example,to the hostname api.foo.bar.example). The mapping configures your cluster's DNS server to return a CNAME record with that external hostname value.No proxying of any kind is set up.
  



  
  ---------------------------------------------
  Ingress
Make your HTTP (or HTTPS) network service available using a protocol-aware configuration mechanism, that understands web concepts like URIs, hostnames, paths, and more. The Ingress concept lets you map traffic to different backends based on rules you define via the Kubernetes API.

FEATURE STATE: Kubernetes v1.19 [stable]

An API object that manages external access to the services in a cluster, typically HTTP.

Ingress may provide load balancing, SSL termination and name-based virtual hosting.

Note:

The Kubernetes project recommends using Gateway instead ofIngress.The Ingress API has been frozen.

This means that:

    The Ingress API is generally available, and is subject to the stability guarantees for generally available APIs.The Kubernetes project has no plans to remove Ingress from Kubernetes.
    The Ingress API is no longer being developed, and will have no further changes or updates made to it.
  
  
  

# Kubernetes Workloads

## What is a Workload?
A workload is any application running in Kubernetes.  
It can be a single service or multiple components working together.  
All workloads run inside Pods.

---

## Pod
A Pod is the smallest deployable unit in Kubernetes.  
It represents one or more running containers in the cluster.  
Containers in a Pod share network and storage.

Pods have a lifecycle and are temporary.  
If a node fails, all Pods on that node are lost.  
Kubernetes does not recover the same Pod; it creates a new one.

---

## Why not manage Pods directly?
Pods are short-lived and can fail at any time.  
Managing them manually is not scalable and is error-prone.

---

## Workload Resources (Controllers)
Kubernetes provides higher-level resources to manage Pods.  
You define the desired state (e.g. number of Pods).  
Controllers ensure the cluster matches that state.

They automatically:
- recreate failed Pods  
- scale Pods up or down  

---

## Built-in Workload Types

### Deployment
You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate.
Used for stateless applications.  
Pods are identical and interchangeable.  
If a Pod fails, it is replaced automatically.  
Supports scaling and rolling updates.

---

### StatefulSet
Used for stateful applications.  
Each Pod has a stable identity and name.  
Each Pod is associated with persistent storage (PersistentVolume).  
Pods are not interchangeable.  
Used for databases or systems that replicate data.

---

### DaemonSet
Ensures one Pod runs on each node.  
When a new node is added, a Pod is scheduled automatically.  
Used for node-level services like logging, monitoring, or networking.

---

### Job
Runs a task until completion.  
Used for one-time tasks such as batch processing.  
Stops after the task is completed.

---

### CronJob
Runs Jobs on a schedule.  
Used for recurring tasks like backups or reports.

---

## Controllers Concept
Controllers continuously monitor the cluster state.  
They compare the actual state with the desired state.  
If there is a difference, they take action to fix it.

---

## Extending Workloads
Kubernetes allows custom workload types using Custom Resource Definitions (CRDs).  
These can provide additional behaviors not available by default.

Example: run a group of Pods only if all can run together.

---

## Workload Placement (Advanced)
By default, Pods are scheduled independently.  

Some workloads require all Pods to run together.  
This is useful for distributed or high-performance tasks.

Gang scheduling ensures:
- all Pods start together, or  
- none of them start  

This feature is advanced and not enabled by default.

---

## Key Points
Pods are temporary and replaceable.  
Workload resources manage Pods automatically.  
Deployment is the default choice for most applications.  
StatefulSet is used when data and identity are required.  
Advanced scheduling requires additional configuration.
  




Use Case

The following are typical use cases for Deployments:

    Create a Deployment to rollout a ReplicaSet. The ReplicaSet creates Pods in the background. Check the status of the rollout to see if it succeeds or not.
    Declare the new state of the Pods by updating the PodTemplateSpec of the Deployment. A new ReplicaSet is created, and the Deployment gradually scales it up while scaling down the old ReplicaSet, ensuring Pods are replaced at a controlled rate. Each new ReplicaSet updates the revision of the Deployment.
    Rollback to an earlier Deployment revision if the current state of the Deployment is not stable. Each rollback updates the revision of the Deployment.
    Scale up the Deployment to facilitate more load.
    Pause the rollout of a Deployment to apply multiple fixes to its PodTemplateSpec and then resume it to start a new rollout.
    Use the status of the Deployment as an indicator that a rollout has stuck.
    Clean up older ReplicaSets that you don't need anymore
  
  
  
# Kubernetes Configuration

## Overview
Kubernetes provides resources to configure how Pods run.  
These control environment variables, secrets, health checks, resources, and cluster access.

---

## ConfigMaps
Used to store non-sensitive configuration data.  
Data is stored as key-value pairs.

Common uses:
- environment variables  
- configuration files  
- application settings  

Pods can consume ConfigMaps as:
- environment variables  
- mounted files  

---

## Secrets
Used to store sensitive data securely.  

Examples:
- passwords  
- API keys  
- tokens  

Similar to ConfigMaps, but:
- data is base64 encoded  
- intended for confidential information  

Secrets can be used in Pods as:
- environment variables  
- mounted files  

---

## Probes (Health Checks)

Kubernetes uses probes to check container health.

### Liveness Probe
Checks if a container is still running correctly.  
If it fails, the container is restarted.

---

### Readiness Probe
Checks if a container is ready to receive traffic.  
If it fails, the Pod is removed from the Service.

---

### Startup Probe
Checks if the application has started successfully.  
Useful for slow-starting containers.  
Disables other probes until startup is complete.

---

## Resource Management

Used to control CPU and memory usage.

Each container can define:

- **requests**: minimum resources required  
- **limits**: maximum resources allowed  

Example:
- scheduler uses requests to place Pods  
- limits prevent overuse of resources  

---

## kubeconfig (Cluster Access)

kubeconfig is a file used to access Kubernetes clusters.  

It contains:
- cluster information  
- user credentials  
- context (which cluster to use)  

Used by tools like:
- kubectl  

Allows switching between multiple clusters easily.

---

## Key Points
ConfigMaps store non-sensitive configuration.  
Secrets store sensitive data.  
Probes monitor container health.  
Resources control CPU and memory usage.  
kubeconfig manages access to clusters.



----------------
# Kubernetes Security Checklist

This is a basic checklist to help you secure your Kubernetes clusters. It provides starting guidance along with links to more detailed documentation. It is a living document and will evolve over time.

**How to use this guide:**
- Topics are **not** listed in order of priority.
- You can find more details for specific items in the paragraphs below each section.

> [!WARNING]
> **Caution:** A checklist alone won't secure your cluster. True security is an ongoing process.
> 
> Keep in mind:
> - This list is just your first step toward better security.
> - Kubernetes security isn't "one size fits all." Some recommendations might be too strict or too relaxed for your specific setup.
> - You should evaluate each item based on your own security needs.


---------
https://kubernetes.io/docs/tasks/





## 1. Start Minikube
```sh
minikube start
```

## 2. Working with Pods
Pods are the smallest deployable units in Kubernetes, representing a single instance of a running process.
```sh
kubectl run --help
kubectl run nginx --image=nginx
kubectl describe pod nginx
kubectl get pods -o wide

# Generate YAML for Redis pod
kubectl run redis --image=redis --dry-run=client -o yaml
kubectl run redis --image=redis --dry-run=client -o yaml > redis.yaml

# Create pod from YAML
kubectl create -f redis.yaml

# Apply changes (if there are modifications)
kubectl apply -f redis.yaml

# Execute a command inside a running pod
kubectl exec -it <pod_name> -- curl -I http://localhost
```

## 3. Working with ReplicaSets
ReplicaSets ensure a specified number of identical Pods are running at all times.
```sh
kubectl get replicaset
kubectl describe replicaset <replicaset_name>
kubectl explain replicaset
```

### Creating a ReplicaSet via YAML
1. Generate a deployment YAML
```sh
kubectl create deployment example-replicaset --image=nginx:1.25 --replicas=3 --dry-run=client -o yaml
```
2. Modify the YAML file to specify `ReplicaSet`:
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: example-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
```
3. Apply the configuration:
```sh
kubectl apply -f example-replicaset.yaml
```

### ReplicaSet Commands
```sh
kubectl edit rs <replicaset_name>
kubectl scale rs <replicaset_name> --replicas=5
```

### Dry Run Modes
- `--dry-run=client`: Validates client-side, ensuring syntax correctness.
- `--dry-run=server`: Validates against the live cluster without applying changes.

## 4. Working with Deployments
Deployments provide declarative updates for Pods and ReplicaSets, allowing for easy scaling and rolling updates.
```sh
kubectl get deploy
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
kubectl create deployment example-deployment --image=nginx:1.25 --replicas=3
kubectl create deployment example-deployment --image=nginx:1.25 --replicas=3 --dry-run=client -o yaml
```

---
This cheat sheet provides quick Kubernetes commands for Pods, ReplicaSets, and Deployments.
