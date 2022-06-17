# Example Script Usage

To set up the a demo environment using 'golden images' on OpenShift:
```
sh scripts/demo-openshift-operators.sh app demo reset 
```

To set up the a demo environment using 'golden images' on Kubernetes:
```
sh scripts/demo-kubernetes-operators.sh app demo reset 
```

To build all images, push to a registry and deploy to OpenShift:
```
sh scripts/ci-create-operators-operators.sh app local reset podman_reset
```

To build all images, push to a registry and deploy to Kubernetes:
```
sh scripts/ci-create-operators-kubernetes.sh app local reset podman_reset
```

For further information, refer to the [reference documentation of the scripts and their parameters](./automation-parameter-reference.md).