# Database Operator - Operator deployed with OLM

🔴 IMPORTANT: First install the [prerequistes](./prerequisites.md)! If you don't do it, it won't work :)

🔴 IMPORTANT: Webhooks and Prometheus doesn't work in this configuration yet.

### Deploy catalog source and subscription

```shell
$ cd operator-database
```

For Kubernetes:

```shell
$ kubectl apply -f olm/catalogsource.yaml
$ kubectl apply -f olm/subscription.yaml 
```

For OpenShift:

```shell
$ kubectl apply -f olm/catalogsource-openshift.yaml
$ kubectl apply -f olm/subscription-openshift.yaml 
```

### Verify the setup

For Kubernetes:

```shell
$ export NAMESPACE=operators
```

For OpenShift:

```shell
$ export NAMESPACE=openshift-operators
$ kubectl get all -n $NAMESPACE
$ kubectl get catalogsource operator-database-catalog -n $NAMESPACE -oyaml
$ kubectl get subscriptions operator-database-v0-0-1-sub -n $NAMESPACE -oyaml
$ kubectl get csv operator-database.v0.0.1 -n $NAMESPACE -oyaml
$ kubectl get installplans -n $NAMESPACE
$ kubectl get installplans install-xxxxx -n $NAMESPACE -oyaml
$ kubectl get operators operator-database.$NAMESPACE -n $NAMESPACE -oyaml
$ kubectl create ns database   
$ kubectl apply -f config/samples/database.sample_v1alpha1_database.yaml
$ kubectl apply -f config/samples/database.sample_v1alpha1_databasecluster.yaml
$ kubectl get databases/database -n database -oyaml
$ kubectl get databases.database.sample.third.party/database -n database -oyaml
```

### Delete all resources

```shell
$ kubectl delete -f config/samples/database.sample_v1alpha1_databasecluster.yaml
$ kubectl delete -f config/samples/database.sample_v1alpha1_database.yaml
$ kubectl delete -f olm/subscription.yaml
$ kubectl delete -f olm/catalogsource.yaml
$ kubectl delete -f olm/subscription-openshift.yaml
$ kubectl delete -f olm/catalogsource-openshift.yaml
```

### Build and push new operator image

Create versions_local.env and change 'REGISTRY', 'ORG' and image version.

```shell
$ source ../versions_local.env
$ podman build -t "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR" .
$ podman push "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR"
```

### Build and push new bundle image

Create versions_local.env and change 'REGISTRY', 'ORG' and image version.

```shell
$ source ../versions_local.env
$ make bundle IMG="$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR"
$ podman build -f bundle.Dockerfile -t "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR_BUNDLE" .
$ podman push "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR_BUNDLE"
```

### Build and push new catalog image

### a) Setup of the needed executable bin files

   Setup of the needed bin files (controller-gen, kustomize, opm, setup-envtest) for the operator-sdk projects. The script will create a temp operator sdk project, to create a the bin file and delete that temp project when it was finished.

   ```shell
   sh scripts/check-binfiles-for-operator-sdk-projects.sh
   ```

  > Note: You need to interact with the script, because when you create the first time a bundle. These are the temp values you can use for the script execution. These are the example values: `'Display name   : myproblemfix'`, `Description    : myproblemfix`, `Provider's name: myproblemfix`, `Any relevant URL:`, `Comma-separated keywords   : myproblemfix` `Comma-separated maintainers: myproblemfix@myproblemfix.net`. 

Example output:
```shell
***  Bin folder status: operator-database
controller-gen  kustomize       opm             setup-envtest
***  Bin folder status: operator-database
controller-gen  kustomize       opm             setup-envtest
```

### b) Build and push

```shell
$ source ../versions_local.env
$ ./bin/opm index add --build-tool podman --mode semver --tag "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR_CATALOG" --bundles "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR_BUNDLE"
$ podman push "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR_CATALOG"
```

> Note: Define "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR_CATALOG" in olm/catalogsource.yaml and/or olm/catalogsource-openshift.yaml and invoke the commands above to apply catalog source and subscription.

### Alternative

The Operator SDK provides a way to deploy the operator without a catalog.

```shell
$ operator-sdk run bundle "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR_BUNDLE" -n operators
or for OpenShift:
$ operator-sdk run bundle "$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR_BUNDLE" -n openshift-operators
```



# Application Operator - Operator deployed with OLM

🔴 IMPORTANT: First install the [prerequistes](./prerequisites.md)! If you don't do it, it won't work :)

### Deploy database operator

Before running the application operator, the database operator needs to be deployed since it is defined as dependency. Follow the [instructions](DbSetupWithoutOLM.md) in the documentation.

### Deploy catalog source and subscription

```shell
$ cd operator-application
```

For Kubernetes:

```shell
$ kubectl apply -f olm/catalogsource.yaml
$ kubectl apply -f olm/subscription.yaml 
```

For OpenShift:

```shell
$ kubectl apply -f olm/catalogsource-openshift.yaml
$ kubectl apply -f olm/subscription-openshift.yaml 
```

### Verify the setup

For Kubernetes:

```shell
$ export NAMESPACE=operators
```

For OpenShift:

```shell
$ export NAMESPACE=openshift-operators
$ kubectl get all -n $NAMESPACE
$ kubectl get catalogsource operator-application-catalog -n $NAMESPACE -oyaml
$ kubectl get subscriptions operator-application-v0-0-1-sub -n $NAMESPACE -oyaml
$ kubectl get csv operator-application.v0.0.1 -n $NAMESPACE -oyaml
$ kubectl get installplans -n $NAMESPACE
$ kubectl get installplans install-xxxxx -n $NAMESPACE -oyaml
$ kubectl get operators operator-application.$NAMESPACE -n $NAMESPACE -oyaml
$ kubectl apply -f config/samples/application.sample_v1beta1_application.yaml
$ kubectl get applications.application.sample.ibm.com/application -n application-beta -oyaml
$ kubectl exec -n application-beta $(kubectl get pods -n application-beta | awk '/application-deployment-microservice/ {print $1;exit}') --container application-microservice -- curl http://localhost:8081/hello
$ kubectl logs -n $NAMESPACE $(kubectl get pods -n $NAMESPACE | awk '/operator-application-controller-manager/ {print $1;exit}') -c manager
```

### Delete all resources

```shell
$ kubectl delete -f config/samples/application.sample_v1beta1_application.yaml
$ kubectl delete -f olm/subscription.yaml
$ kubectl delete -f olm/catalogsource.yaml
$ kubectl delete -f olm/subscription-openshift.yaml
$ kubectl delete -f olm/catalogsource-openshift.yaml
```

### Build and push new bundle image

Create versions_local.env and change 'REGISTRY', 'ORG' and image version.

```shell
$ source ../versions_local.env
$ make bundle IMG="$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR"
$ podman build -f bundle.Dockerfile -t "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR_BUNDLE" .
$ podman push "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR_BUNDLE"
```

### Build and push new catalog image

Create versions_local.env and change 'REGISTRY', 'ORG' and image version.

```shell
$ source ../versions_local.env
$ ./bin/opm index add --build-tool podman --mode semver --tag "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR_CATALOG" --bundles "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR_BUNDLE"
$ podman push "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR_CATALOG"
```

Define "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR_CATALOG" in olm/catalogsource.yaml and/or olm/catalogsource-openshift.yaml and invoke the commands above to apply catalog source and subscription.

### Alternative

The Operator SDK provides a way to deploy the operator without a catalog.

```shell
$ operator-sdk run bundle "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR_BUNDLE" -n operators
or for OpenShift:
$ operator-sdk run bundle "$REGISTRY/$ORG/$IMAGE_APPLICATION_OPERATOR_BUNDLE" -n openshift-operators
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