# Deploy the `Application Operator`

The `Application Operator` depends on the `Database Operator`. 

### Step 1: Install the database operator without OLM

```sh
cd operator-database
source ../versions.env
make deploy IMG="$REGISTRY/$ORG/$IMAGE_DATABASE_OPERATOR"
kubectl apply -f config/rbac/role_patch.yaml 
kubectl apply -f config/rbac/role_binding_patch.yaml
```

### Step 2: Create a database instance

```sh
kubectl apply -f config/samples/database.sample_v1alpha1_database.yaml
```