# Demo Setup

Although the operators have been tested with IBM Cloud Kubernetes Service, the demo video and descriptions in this section relate only to Red Hat OpenShift.  If you wish to test the sample operators, then follow these mostly automated steps to setup an OpenShift cluster ready for demonstration.

#### Clone the [repo](https://github.com/IBM/operator-sample-go)
#### Provision an [IBM Cloud OpenShift service](./dev-setup-openshift.md)
#### Install the workstation [prerequistes](./dev-prerequisites.md)
#### Install all demo components to OpenShift

```
cd scripts
sh demo-openshift-operators.sh app demo reset
```

#### Create a Cloud Object Storage (COS) Bucket and Access Secrets

Create Object Storage on [IBM Cloud](https://cloud.ibm.com/)

1. Login to your IBM Cloud Console
2. Search for Object Storage in Search Bar or Click on Catalog from Upper Navigation Menu, Choose Services in Type
3. Click on the Object Storage 
4. Once clicked a tab window will open of Cloud Ojbect Storage, IBM Cloud will be selected by default
5. Choose your pricing plan
6. In configure your resource section:
    * Enter Service Name
    * Select a Resource Group
    * Enter Tags as requied
7. Click on Create

Create service credentials

1. From the Left Navigation Menu Click on "Service Credentials".
2. Click the "New Credential" blue button.
3. Enter the name of credentials.
4. Keep the Role to "writer".
5. Click on "Adavanced options".
6. Enable to the toggle to "Include HMAC Credentials".
7. Click the "Add" button.
8. Expand the credential name to show the "ACCESS KEY ID" and  "SECRET ACCESS KEY" written in the JSON.

Store the service credentials to a Kubernetes Secret. This is required for the operator-database-backup application which backs up data to Cloud Object Storage

1. Navigate to the [secret.yaml](https://github.com/IBM/operator-sample-go/blob/main/operator-database-backup/kubernetes/secret.yaml) location (`operator-database-backup/kubernetes`) and open the file.

```sh
operator-database-backup/kubernetes
nano secret.yaml
```

2. Edit file [secret.yaml](https://github.com/IBM/operator-sample-go/blob/main/operator-database-backup/kubernetes/secret.yaml) and change the following lines respectively:

```
HmacAccessKeyId: "Add access_key_id from Credential JSON"
HmacSecretAccessKey: "Add ecret_access_key from Credential JSON"
```

3. Create the secret on the cluster.

```sh
kubectl apply -f secret.yaml
```

#### Create a Route to the /hello enpoint for the micro-service application

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
4. Click the "Create Route" button
5. Add following details
    * Name the route "hello"
    * Add the path "/hello"
    * Select Service "application-service-microservice"
    * Select Target Port 8081 -> 8081 (TCP)
    * Click on "Create"

Now browse to the Location Link provided in the Routes List to invoke the /hello endpoint.  It sometimes takes a couple of requests before the simple-microservice application responds.  The browser should render the following response:

```
Hello World and hello Adam
```

The Route will continue to work even if the simple-microservice application is deleted and later re-created (by the Application operator)

#### Uninstall components prior to demo

Having followed the above steps, you should have an OpenShift cluster with all the required components to test the sample operators.  However, in the demo video, certain steps were performed manually via the OpenShift console e.g. installing the operators via OperatorHub.  If you wish to reproduce the demo yourself, it is necessary to delete some resources in preparation:

* Uninstall both operators (but leave Catalog sources), using OpenShift Console menu ```Installed Operators```
* Delete all CR instances (if present): Application, Database, Databasecluster, Databasebackup, using using OpenShift Console menu ```Administration->CustomResourceDefinitions->[CRD Name]->Instances```
* Delete all CRDs: Application, Database, Databasecluster, Databasebackup, using using OpenShift Console menu ```Administration->CustomResourceDefinitions```
