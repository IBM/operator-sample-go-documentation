# Deploy the `Application Operator`

The `Application Operator` depends on the `Database Operator`. 

### Install `Database Operator` without OLM

#### Step 1: Install the database operator without OLM

```sh
cd operator-database
source ../versions.env
make deploy IMG="$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR"
kubectl apply -f config/rbac/role_patch.yaml 
kubectl apply -f config/rbac/role_binding_patch.yaml
```

#### Step 2: Create a database instance

```sh
kubectl apply -f config/samples/database.sample_v1alpha1_database.yaml
```

### Install Application Operator

```sh
cd ..
cd operator-application
```

#### Step 1: 

```sh
kubectl apply -f olm/catalogsource-openshift.yaml
kubectl apply -f olm/subscription-openshift.yaml
```