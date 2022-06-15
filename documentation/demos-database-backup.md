# 3.3 Database backup

### Creating a Database Cluster

Before we can explore how the database operator automates backup, we first need to create an instance of the database. This is achieed by create a resources with a ‘kind’ that correlates to the CRD definition already installed by the database operator. Resources can be created manually via the operator’s UI, or using a yaml file.

The demo show how to trigger the operator by creating an instance of the [DatabaseCluster resource](https://github.com/IBM/operator-sample-go/blob/main/operator-database/config/samples/database.sample_v1alpha1_databasecluster.yaml).

Note the kind is a custom resource, defined by our Database operator, and the fields are specific to our database application.  We don’t need to create lots of kuberenetes resources ourselves, the DatabaseCluster kind provides an abstraction. The operator will reconsile this resource and create multiple Kubernetes resources (i.e. StatefulSet, Service etc) in the database namespace.  One pod is the leader supporting reads and writes, the others are followers provided only read access using replicated data.

The database cluster has populated itself with some default data. It provides as endpoint to return the data (a simple JSON file containing a list of names):

<img src="images/demo11.png" />

### Creating a Database Backup


### How Does it Work?


