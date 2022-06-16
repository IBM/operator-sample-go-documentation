# 3.1 Demos Overview

The following pages summarise the key points for each use case featured in the [demo video]((https://www.youtube.com/watch?v=D6njEyXPieg).  In addition, a more detailed technical explanation of each use case is provided.

*[Introduction to OpenShift OperatorHub, packaging with OLM & Installing operators from OperatorHub](./demos-operatorhub.md)

*[Database Backup](./demos-database-backup.md)

*[Application Auto-scaling](./demos-auto-scaler.md)

### Demo Setup

Although the operators have been tested with IBM Cloud Kubernetes Service, the demo video and descriptions in this section relate only to Red Hat OpenShift.  If you wish to test the sample operators, then follow these mostly automated steps to setup an OpenShift cluster ready for demonstration.

#### Clone the [repo](https://github.com/IBM/operator-sample-go)
#### Provision an [IBM Cloud OpenShift service](./dev-setup-openshift.md)
#### Install the workstation [prerequistes](./prerequisites.md)
#### Install all demo components to OpenShift

```
cd scripts
sh scripts/demo-openshift-operators.sh app demo reset
```

### Create a Cloud Object Storage (COS) Bucket and Access Secrets

Create Object Storage on [IBM Cloud](https://cloud.ibm.com/)

1. Login to your IBM Cloud Console
2. Search for Object Storage in Search Bar **_OR_** Click on Catalog from Upper Navigation Menu, Choose Services in Type
3. Click on the Object Storage 
4. Once clicked a tab window will open of Cloud Ojbect Storage, **IBM Cloud** will be selected by default
5. Choose your pricing plan
6. in configure your resource section:
a. Enter Service Name
b. Select a Resource Group
c. enter Tags as requied
7. Click on Create

Create service credentials

1. From the Left Navigation Menu Click on **Service Credentials**.
2. in the righ Middle Section click on **"New Credential"** Blue Button.
3. Enter the name of credentials.
4. Keep the Role to **"writer"**.
5. Click on **"Adavanced options"**.
6. Enable to the toggle to **"Include HMAC Credentials"**.
7. Click on **"Add"** Button.
8. Click on the **Arrow Down** before credential name to show the **ACCESS KEY ID** and  **SECRET ACCESS KEY** written in **JSON**.

Store the service credentials to a Kubernetes Secret. This is required for the operator-database-backup application which backs up data to Cloud Object Storage

1. Edit file [secret.yaml](https://github.com/IBM/operator-sample-go/blob/main/operator-database-backup/kubernetes/secret.yaml) and change the following lines respectively:

```
HmacAccessKeyId: "ADD access_key_id from Credential JSON"
HmacSecretAccessKey: "ADD ecret_access_key from Credential JSON"
```

2. Create the secret on the cluster

```
cd operator-database-backup
kubectl apply -f kubernetes/secret.yaml
```

### Create a Route to the /hello enpoint for the micro-service application

Before you can create and test the route, you must have deployed the Application and DatabaseCluster resources.

```
cd operator-database
oc apply -f config/samples/database.sample_v1alpha1_databasecluster.yaml
```
```
cd operator-application
oc apply -f config/samples/application.sample_v1beta1_application.yaml
```

1. Login to Openshift console
2. Go to the Networking section in left navigation 
3. Select Routes
4. Click on Create Route button
5. Add Following details
a. Name of the route "hello"
b. add the path "/hello"
c. Select Service "application-service-microservice"
d. Select Target Port 8081 -> 8081 (TCP)
e. Click on Create

Now browse to the Location Link provided in the Routes List to invoke the /hello endpoint.  It sometimes takes a couple of requests before the simple-microservice application responds.  The browser should render the following response:

```
Hello World and hello Adam
```

The Route will continue to work even if the simple-microservice application is deleted and later re-created (by the Application operator)

### Uninstall components to prepare for demo

Having followed the above steps, you should have an OpenShift cluster with all the required components to perform a demo.  However, in the demo video, certain steps were performed manually via the OpenShift console e.g. installing the operators via OperatorHub.  If you wish to reproduce the demo yourself, it is necessary to delete some resources in preparation:

* Uninstall both operators (but leave Catalog sources), using OpenShift Console menu ```Installed Operators```
* Delete all CR instances (if present): Application, Database, Databasecluster, Databasebackup, using using OpenShift Console menu ```Administration->CustomResourceDefinitions```
* Delete all CRDs: Application, Database, Databasecluster, Databasebackup, using using OpenShift Console menu ```Administration->CustomResourceDefinitions```