# Database Operator - Operator deployed without OLM

🔴 IMPORTANT: First install the [prerequistes](Prerequisites.md)! If you don't do it, it won't work :)

### Deploy database operator

```shell
$ cd operator-database
$ source ../versions_local.env
$ make deploy IMG="$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR"
$ kubectl apply -f config/rbac/role_patch.yaml 
$ kubectl apply -f config/rbac/role_binding_patch.yaml 
```

From a terminal run this command:

```shell
$ kubectl create namespace database
$ kubectl apply -f config/samples/database.sample_v1alpha1_database.yaml
$ kubectl apply -f config/samples/database.sample_v1alpha1_databasecluster.yaml
$ kubectl get databases/database -n database -oyaml
```

### Delete all resources

```shell
$ kubectl delete -f config/samples/database.sample_v1alpha1_databasecluster.yaml
$ kubectl delete -f config/samples/database.sample_v1alpha1_database.yaml
$ make undeploy IMG="$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR"
```

### Build and push new image

Create versions_local.env and change 'REGISTRY', 'ORG' and image version.

```shell
$ cp ../versions.env ../versions_local.env
(fill appropriate values for 'REGISTRY' & 'ORG')
$ source ../versions_local.env
$ podman build -t "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR" .
$ podman push "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR"
```



# Application Operator - Operator deployed without OLM

🔴 IMPORTANT: First install the [prerequistes](Prerequisites.md)! If you don't do it, it won't work :)

### Deploy database operator

Before running the application operator, the database operator needs to be deployed since it is defined as dependency. Follow the [instructions](DbSetupWithoutOLM.md) in the documentation.

### Deploy the operator

```shell
$ cd operator-application
$ source ../versions.env
$ make deploy IMG="$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR"
$ kubectl apply -f config/rbac/role_patch.yaml 
$ kubectl apply -f config/rbac/role_binding_patch.yaml 
```

### Create an application resource

```shell
$ kubectl apply -f config/samples/application.sample_v1beta1_application.yaml
```

### Verify the setup

```shell
$ kubectl get all -n operator-application-system
$ kubectl get applications.application.sample.ibm.com/application -n application-beta -oyaml
$ kubectl exec -n application-beta $(kubectl get pods -n application-beta | awk '/application-deployment-microservice/ {print $1;exit}') --container application-microservice -- curl http://localhost:8081/hello
$ kubectl logs -n operator-application-system $(kubectl get pods -n operator-application-system | awk '/operator-application-controller-manager/ {print $1;exit}') -c manager
```

### Delete all resources

```shell
$ kubectl delete -f config/samples/application.sample_v1beta1_application.yaml
$ make undeploy IMG="$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR"
```

### Build and push new image

Create versions_local.env and change 'REGISTRY', 'ORG' and image version.

```shell
$ source ../versions_local.env
$ podman build -t "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR" .
$ podman push "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR"
```

### Test the conversions between v1alpha1 and v1beta1

v1alpha1:

```shell
$ kubectl apply -f config/samples/application.sample_v1alpha1_application.yaml
$ kubectl delete -f config/samples/application.sample_v1alpha1_application.yaml
$ kubectl exec -n application-alpha $(kubectl get pods -n application-alpha | awk '/application-deployment-microservice/ {print $1;exit}') --container application-microservice -- curl http://localhost:8081/hello
$ kubectl get applications.v1alpha1.application.sample.ibm.com/application -n application-alpha -oyaml | grep -A6 -e "spec:" -e "apiVersion: application.sample.ibm.com/" 
$ kubectl get applications.v1beta1.application.sample.ibm.com/application -n application-alpha -oyaml | grep -A6 -e "spec:" -e "apiVersion: application.sample.ibm.com/" 
```

v1beta1:

```shell
$ kubectl apply -f config/samples/application.sample_v1beta1_application.yaml
$ kubectl delete -f config/samples/application.sample_v1beta1_application.yaml
$ kubectl exec -n application-beta $(kubectl get pods -n application-beta | awk '/application-deployment-microservice/ {print $1;exit}') --container application-microservice -- curl http://localhost:8081/hello
$ kubectl get applications.v1alpha1.application.sample.ibm.com/application -n application-beta -oyaml | grep -A6 -e "spec:" -e "apiVersion: application.sample.ibm.com/" 
$ kubectl get applications.v1beta1.application.sample.ibm.com/application -n application-beta -oyaml | grep -A6 -e "spec:" -e "apiVersion: application.sample.ibm.com/" 
```

### Prometheus Metrics

Only needed for OpenShift:

These steps allow the default Prometheus instance on OpenShift to monitor the resources deployed by the application operator.  In addition, because this instance is used to monitor other k8s resources, it requires authentication and can only be accessed via https.  Therefore additional secrets must be created providing a certificate and bearer token which are used by the [application scaler](../operator-application-scaler/README.md) job to access the Prometheus API.  Additional RBAC permissions are also required.

```shell
$ oc label namespace application-beta openshift.io/cluster-monitoring="true"
$ cd operator-application
$ kubectl apply -f prometheus/role-openshift.yaml
$ oc get secrets -n openshift-ingress
```
Locate the default TLS secret with type 'kubernetes.io/tls', e.g. 'deleeuw-ocp-cluster-162e406f043e20da9b0ef0731954a894-0000'
```shell
oc extract secret/<default TLS secret for your cluster> --to=/tmp -n openshift-ingress
kubectl create secret generic prometheus-cert-secret --from-file=/tmp/tls.crt -n application-beta
oc sa get-token -n openshift-monitoring prometheus-k8s > /tmp/token.txt
kubectl create secret generic prometheus-token-secret --from-file=/tmp/token.txt -n application-beta
```

For both OpenShift and Kubernetes:

```shell
$ cd operator-application
$ kubectl apply -f prometheus/role-all.yaml
```

For both OpenShift and Kubernetes, open the Prometheus dashboard:

```shell
$ kubectl port-forward service/prometheus-operated -n monitoring 9090
or for OpenShift:
$ kubectl port-forward service/prometheus-operated -n openshift-monitoring 9090
$ open http://localhost:9090/graph
```

Search for 'reconcile_launched_total' and 'application_net_heidloff_GreetingResource_countHelloEndpointInvoked_total'.