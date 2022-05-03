# Prerequisites

In order to run the example you need following:

1. CLIs on your local machine
2. This source code repository
3. A Kubernetes cluster
4. Installed components on the Kubenetes cluster 
5. Images registry.

### 1. Required CLIs

1. [operator-sdk](https://sdk.operatorframework.io/docs/installation/) (comes with Golang)
2. [git](https://github.com/git-guides/install-git)
3. [kubectl](https://kubernetes.io/de/docs/setup/)
4. [podman](https://podman.io/)
5. Only if IBM Cloud is used: [ibmcloud](https://cloud.ibm.com/docs/cli?topic=cli-install-ibmcloud-cli)

### 1.1. Operator SDK

🔴 IMPORTANT: The repo has been tested with operator-sdk v1.18.0. 

> _Note:_ that there is an issue with later versions. For more details please visite that [blog post](https://suedbroecker.net/2022/04/28/make-generate-error-127/).

### 2. Source code repository

```sh
git clone https://github.com/ibm/operator-sample-go.git
cd operator-sample-go
```

### 3. Kubernetes cluster

You need a Red Hat OpenShift cluster.

```sh
oc login --token=sha256~xxxxx --server=https://c106-e.us-south.containers.cloud.ibm.com:32335
kubectl get all
```

### 4. Required Kubernetes Components




