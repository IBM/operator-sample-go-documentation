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

#### Step 3: Verify the instances

There were two projects created:

1. `operator-database-system`
2. `database`

* databases

```sh
kubectl get databases -n operator-database-system
kubectl get databases -n database
```

* Databasebackups

```sh
kubectl get 
databasebackups -n operator-database-system
kubectl get databasebackups -n database
```

* Databaseclusters

```sh
kubectl get 
databaseclusters -n operator-database-system
kubectl get databaseclusters -n database
```

### Install Application Operator

#### Step 1: Navigate to the `Application Operator`

```sh
cd ..
cd operator-application
```

#### Step 2: Install the `Application Operator` OLM

* Create a catalog source

```sh
kubectl apply -f olm/catalogsource-openshift.yaml
```

* Create a subscription

```sh
kubectl apply -f olm/subscription-openshift.yaml
```

#### Step 3: Verify the installation

```sh
kubectl get 
```
