# KIND Cluster Setup Guide

## 1. Installing KIND and kubectl
Install KIND and kubectl using the provided [script](https://github.com/LondheShubham153/kubestarter/blob/main/kind-cluster/install.sh):

## 2. Setting Up the KIND Cluster
Create a kind-config.yaml file:

```yaml

kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    image: kindest/node:v1.35.1
  - role: worker
    image: kindest/node:v1.35.1
  - role: worker
    image: kindest/node:v1.35.1
```
Create the cluster using the configuration file:

```bash

kind create cluster --config kind-config.yaml --name my-kind-cluster
or
kind create cluster --config=kind-config.yaml --name=my-kind-cluster
```
Verify the cluster:

```bash
kubectl cluster-info --context my-kind-cluster
kubect get node --context my-kind-cluster
```
to avoid mentioning '-- context ' every time run below command mean to change the current/default context via kubectl command:
```bash
kubect config use-context my-kind-cluster
```
then you can run commands like below:
```bash
kubectl get nodes
kubectl cluster-info
```
## 3. Accessing the Cluster
Use kubectl to interact with the cluster:
```bash
kubectl cluster-info
```


## 4. Setting Up the Kubernetes Dashboard
Deploy the Dashboard
Apply the Kubernetes Dashboard manifest:
```bash

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```
Create an Admin User
Create a dashboard-admin-user.yml file with the following content:

```yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
Apply the configuration:

```bash

kubectl apply -f dashboard-admin-user.yml
```
Get the Access Token
Retrieve the token for the admin-user:

```bash

kubectl -n kubernetes-dashboard create token admin-user
```
Copy the token for use in the Dashboard login.

Access the Dashboard
Start the Dashboard using kubectl proxy:

```bash

kubectl proxy
```
Open the Dashboard in your browser:

```bash

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```
Use the token from the previous step to log in.

## 5. Deleting the Cluster
Delete the KIND cluster:
```bash

kind delete cluster --name my-kind-cluster
```

## 6. Notes

Multiple Clusters: KIND supports multiple clusters. Use unique --name for each cluster.
Custom Node Images: Specify Kubernetes versions by updating the image in the configuration file.
Ephemeral Clusters: KIND clusters are temporary and will be lost if Docker is restarted.


----

# kind (short for Kubernetes IN Docker) is an open-source command-line tool used to run local Kubernetes clusters using Docker containers as cluster "nodes". It was primarily created to test Kubernetes itself, though it is widely used for local application development and Continuous Integration (CI) pipelines.
(Note: In Kubernetes YAML manifests, kind is also the standard field name used to define the type of resource being created, such as Pod, Deployment, or Service.)

### How kind Works

Instead of spinning up heavy virtual machines for every node, kind treats containers like virtual nodes.
• Containerized Nodes. Each control plane and worker node runs inside its own Docker container (or alternative container runtime like Podman or nerdctl).
• Pre-built Node Images. Nodes use specialized kindest/node images containing systemd, a container runtime (containerd), and core Kubernetes binaries.
• kubeadm Bootstrapping. kind uses kubeadm inside those containers to configure the cluster. This makes it a CNCF-certified conformant Kubernetes installer that closely mirrors real clusters.

### Key Features and Benefits

• Fast Startup and Cleanup. Clusters typically spin up in under a minute with a single command (kind create cluster) and can be destroyed just as quickly (kind delete cluster).
• Multi-Node Support. You can easily simulate complex topologies—including multiple worker nodes and High Availability (HA) control planes—on a single laptop using a simple YAML configuration file.
• Cross-Platform. It runs seamlessly on Linux, macOS, and Windows.
• Custom Builds. Developers contributing to Kubernetes can build node images directly from Kubernetes source code to test changes locally.

### Basic Commands

To get started using the official kind Documentation, you only need Docker and the kind CLI installed:
• Create a default cluster. kind create cluster
• Create a named cluster. kind create cluster --name my-cluster
• Delete a cluster. kind delete cluster
