# Version References

The following information defines the environments in which the scripts have been tested, and tested versions of the prerequisite components.

An explanation of how the scripts work is also provided.

### Tested workstation environments

The scripts have been validated for the following technical environments:

| OS| Version | Tested |
| --- | ---  |  --- |
| macOS | 12.3.1 | OK |
| macOS | 11.2.3 | OK |


### Tested version of tools and frameworks for the prerequsites

| Tools or frameworks | Version/s |  (G)obal, (L) ocal or (C)loud installed | Tested |
| --- | --- | --- |  --- |
| [Podman](https://podman.io/) Client / Server | 4.1.0 / 4.3.0 | G | OK |
| [Operator SDK](https://sdk.operatorframework.io/) | v1.18.0,  v1.18.1, v1.19.1 | G | OK |
| Go | go1.17.6 _(operator-sdk v1.18.0, v1.18.1 )_ (worked also with v1.19.1 ) | G | OK |
| Go | go1.17.8 _(operator-sdk v1.19.1)_ | G | NOT TESTED |
| Kubernetes cluster (VPC) | 1.23.6_1527 | C | OK |
| kubectl client | 1.23 | L | OK |
| kubectl server | v1.23.6+IKS | C | OK |
| oc server | 4.7.0 | C | OK |
| oc client | 4.10.9 | C | OK |
| operator-database/bin/[opm](https://github.com/operator-framework/operator-registry/blob/master/docs/design/opm-tooling.md#opm) | v1.21.0 | L | OK |
| operator-application/bin/[opm](https://github.com/operator-framework/operator-registry/blob/master/docs/design/opm-tooling.md#opm) | v1.21.0 | L | OK |
| bash | GNU bash, version 3.2.57(1)-release (x86_64-apple-darwin21)  | L | OK |
| sed | 12.3.1 | L | OK |
| awk | awk version 20200816 | L | OK |
| cURL | 7.79.1 | L | OK |
| grep | 2.6.0-FreeBSD | L | OK |
| tar | bsdtar 3.5.1 | L | OK |
| container registry | [DockerHub](https://hub.docker.com/), [Quay(Red Hat)](https://quay.io/) | C | OK |


| IBM Cloud Services used| Version | Tested |
| --- | ---  |  --- |
| [IBM Cloud Kubernetess Service](https://cloud.ibm.com/kubernetes/catalog/create) (on VPC)| v1.23.6+IKS |  OK |
| [Red Hat OpenShift on IBM Cloud](https://www.ibm.com/cloud/openshift) (on VPC)| 4.10.9_1515 |  OK |
| [IBM Cloud Object Storage](https://cloud.ibm.com/objectstorage/create) | TBD |  TBD |
| [IBM Cloud 'Virtual Private Cloud'](https://cloud.ibm.com/vpc-ext/provision/vpc) | TBD |  TBD |


### Reference: Known problems

Table of verifications

| Operator | Cluster | Type | Region | Security Group | Public Gateway | Container Registry | Tested | Note |
| --- | --- | --- |  --- |  --- | --- | --- |  --- | --- |
| Database | Kubernetes (1.23.6_1527) | VPC | us-south  | Validated | Validated | `Quay.io` | OK | |
| Application (microservice) | Kubernetes (1.23.6_1527) | VPC | us-south | Validated | Validated | `DockerHub` |  Problems | Can't be pulled from DockerHub. Solution delete repo on DockerHub and recreate it. |
| Application | OpenShift |  VPC | us-south | Validated | Validated | `DockerHub` and `Quay`|  Problems | Instance of `operators.coreos.com` can't be deleted. |
| Application | OpenShift |  VPC | us-south | Validated | Validated | `DockerHub` and `Quay`|  Problems | Operator instance can't be created when using the automation script. |