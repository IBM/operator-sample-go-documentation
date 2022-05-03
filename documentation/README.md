# Operator sample go documentation (custom)

**UNDER CONSTRUCTION**

The project [Operator sample go](https://github.com/IBM/operator-sample-go) contains Kubernetes operator samples that demonstrate best practices how to develop operators with [Golang](https://go.dev/), [Operator SDK](https://sdk.operatorframework.io/) including [Kubebuilder](https://github.com/kubernetes-sigs/kubebuilder) and the [Operator Lifecycle Manager Framework](https://operatorframework.io/).

The [Operator sample go](https://github.com/IBM/operator-sample-go) contains two operators and four applications.

### The two operators

* **Application operator**: Deploys and manages a `simple microservice` application.

* **Database operator**: Deploys and manages a simple database. Used by the `database service` application.

### The four applications:

1) [Simple micorservice](simple-microservice/README.md) managed by the application operator

2) [Database service](database-service/README.md) managed by the database operator

3) [Database controller extension](operator-database-backup/README.md) to automatically backups of data

4) [Application controller extension](operator-application-scaler/README.md) to automatically scale up the microservice