# Overview

The [Operator Sample Go](https://github.com/IBM/operator-sample-go) project contains two operators and four applications:

### Application Operator

The application operator deploys and manages a front-end micro-service which provides a simple web UI.  The micro-service provides a single HTTP enpoint:
    
>**/hello** - Renders a Hello greeting to the names persisted in the corresponding database.
    
The application operator is responsible for the following tasks:

#### Deployment of Kubernetes resources:
* Deployment to manage front-end Pods, Service, Roles/Role Bindings, Cronjob (to launch auto-scaling decision logic)
* Database (a Custom Resource reconciled by the database operator, which creates the schema)
#### Auto-pilot operational tasks:
* Auto-scaling of pods according to how many times the /hello endpoint has been invoked

The application operator can be found in the [operator-application](https://github.com/IBM/operator-sample-go/tree/main/operator-application) folder.

### Database Operator

The database operator relates to the backend database which provides a very simple file based database, included in this project.  It was created purely to help demonstrate the capabilities of operators.

The database implements a cluster where one pod is the 'leader' to which applications can perform reads and writes.  Further pods are 'followers' providing high availability and scalability.  Followers synchronize the data from the leader, but 
only provide read capability.  Each pod of the database cluster stores its data to a persistent volume.
    
The database is very opinionated and only supports a single use case - storing a list of first & last names, persisted in a JSON file.  Therefore only a single API is provided:
    
>**/persons** - GET/POST/PUT/DELETE a Person JSON object (other APIs help the pods elect the leader, and synchronize the data)
    
The database operator is responsible for the following tasks:

#### Deployment of Kubernetes resources:
* StatefulSet to manage stateful Pods and their binding to Persistent Volume Claims, Roles/Role Bindings, Service, Cronjob (to launch auto-backup logic)
#### Auto-pilot operational tasks:
* Immediate or scheduled backup of the database to one or more long term repositories (e.g. Cloud Object Storage)

The database operator can be found in the [operator-database](https://github.com/IBM/operator-sample-go/tree/main/operator-database) folder.


### Assosiated Applications & Scripts

The repo provides the following applications, used by the operators

* [simple-microservice](https://github.com/IBM/operator-sample-go/tree/main/simple-microservice) - The front end web application, written in Java using Quarkus
* [database-service](https://github.com/IBM/operator-sample-go/tree/main/database-service) - The simple database application deployed by the database operator, written in Java using Quarkus
* [operator-database-backup](https://github.com/IBM/operator-sample-go/tree/main/operator-database-backup) - A Go application to query the database and upload the data to cloud object storage.  This container is launched on a schedule by the database operator
* [operator-application-scaler](https://github.com/IBM/operator-sample-go/tree/main/operator-application-scaler) - A Go application used to make autoscaling decisions for the front-end.  It queries Prometheus metrics exposed by the simple-microservice, and if necessary, modifies the custom resource which defines the size of the front-end deployment.  This container is launched on a schedule by the application operator

In addition, the repo includes:

* [scripts](https://github.com/IBM/operator-sample-go/tree/main/scripts) - Automation to verify workstation prerequisites, build all container images and deploy to a Kubernetes or OpenShift cluster.  Alternatively, the scripts can deploy pre-built 'golden' container images.

### Next steps to use this project

Get hands-on with the sample operators:

* Setup your workstation with the [prerequisites](./dev-prerequisites.md) for development
* Understand the basic [architecture](./intro-architecture-overview.md) of the operators
* Watch the [demo video](./intro-demo-use-cases.md)
* Understand in detail the [backup](./overview-automatically-archive-data-with-k8s-operators.md) and [scaling](./overview-scalling-applications-automatically-with-operators.md) capabilities
* Use the [script automation](./automation-overview.md) to build all images and deploy to IBM Cloud Kubernetes Service or IBM Cloud OpenShift Service

When you're ready to experiment with changes to the sample operators:

* Learn how to [run & debug an operataor locally](./dev-run-operator-locally.md)
* Learn how to [deploy an operator to a kubernetes cluster without requiring the Operator Lifecycle Manager (OLM) packaging](./dev-run-operators-without-olm.md)
* Learn how to manually [package and deploy an operator using OLM](./dev-run-operator-with-olm.md). This is the approach our script automation uses.

When you're ready to create your own operators, read our companion blogs:

* [Links to operator development resources](http://heidloff.net/articles/resources-to-build-kubernetes-operators/)
* [Basic operator capabilities](./basic-capabilities-overview.md)
* [Advanced operator capabilities](./advanced-capabilities-overview.md) 
* [Go hints & tips related to operator development](./golang-overview.md)