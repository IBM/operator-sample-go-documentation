# Database Operator - Setup and local Usage

🔴 IMPORTANT: First install the [prerequistes](./dev-prerequisites.md)! If you don't do it, it won't work :)

### Run operator locally

From a terminal run this command:

```shell
$ cd operator-database
$ make install run
```

Alternatively, to debug the operator in VSCode, press F5 (Run - Start Debugging) instead of 'make install run'. The directory 'operator-application' needs to be root in VSCode.

From a second terminal run this command to create an instance of the DatabaseCluster and Database custom resources:

```shell
$ kubectl apply -f config/samples/database.sample_v1alpha1_database.yaml
$ kubectl apply -f config/samples/database.sample_v1alpha1_databasecluster.yaml
```

### Delete all resources

```shell
$ kubectl delete -f config/samples/database.sample_v1alpha1_databasecluster.yaml
$ kubectl delete -f config/samples/database.sample_v1alpha1_database.yaml
$ make uninstall
```


# Application Operator - Setup and local Usage

🔴 IMPORTANT: First install the [prerequistes](./prerequisites.md)! If you don't do it, it won't work :)

### Create database custom resource definition

The application operator will not install until the database operator is also installed.  Hence to test the application operator, you must first create the required database custom resource definition.

```shell
$ cd operator-application
$ kubectl create namespace database
$ kubectl apply -f ../operator-database/config/crd/bases/database.sample.third.party_databases.yaml
```

### Run operator locally

From a terminal run this command:

```shell
$ cd operator-application
$ make install run ENABLE_WEBHOOKS=false
```

From another terminal run this command to create an instance of the Application custom resource:

```shell
$ kubectl apply -f config/samples/application.sample_v1beta1_application.yaml
```

Alternatively, to debug the operator in VSCode (without webhooks), press F5 (Run - Start Debugging) instead of 'make install run'. The directory 'operator-application' needs to be root in VSCode.

### Verify the setup

```shell
$ kubectl get applications.application.sample.ibm.com/application -n application-beta -oyaml
$ kubectl exec -n application-beta $(kubectl get pods -n application-beta | awk '/application-deployment-microservice/ {print $1;exit}') --container application-microservice -- curl -s http://localhost:8081/hello
```

### Delete all resources

```shell
$ kubectl delete -f config/samples/application.sample_v1beta1_application.yaml
$ make uninstall
```