# Automation with scripts


Automation scripts are the recommended way to deploy the sample operators.  The following types of scripts are provided:

* Verify workstation prerequisites
* Install required components to a Kubernetes or OpenShift cluster
* Create a 'demo environment' which deploys working golden images to a Kubernetes or OpenShift cluster
* Continous integration deployment which builds all operators & applications, pushes to a registry and deploys using OLM to either Kubernetes or OpenShift


### Required Setup

#### a) Verification of the prerequisites

All local installation versions are related to macOS.

You can run to verify the prerequisites with [the following script](https://github.com/IBM/operator-sample-go/blob/main/scripts/check-prerequisites.sh). The script informs you if the tools are installed, but you need to verify the versions in the terminal output with the version in the table below.

```sh
sh scripts/check-prerequisites.sh
```

#### b) Setup of the required executable `bin` files

The repo does not contain certain bin files which are required to build operators.  The bin files (controller-gen, kustomize, opm, setup-envtest) are normally added to the operator project when initially created by the operator SDK tool.  A script is provided to create a temp operator SDK project, copy the bin files to sample application and database operator projects, then delete the temp project when it has finished.

```sh
sh scripts/check-binfiles-for-operator-sdk-projects.sh
```

> Note: You need to interact with the script. These are the temp values you can use for the script execution: `'Display name   : myproblemfix'`, `Description    : myproblemfix`, `Provider's name: myproblemfix`, `Any relevant URL:`, `Comma-separated keywords   : myproblemfix`
`Comma-separated maintainers: myproblemfix@myproblemfix.net`. 


#### c) Setup the `version.env` and `version_local.env`

If you want to run the samples without modifications, nothing needs to be changed.

If you want to change them, replace `REGISTRY` and `ORG` with your registry account and change the version numbers in `versions_local.env` file. 

* Create a `version_local.env` file based on the template.

```sh
cat versions_local.env-template > versions_local.env
```

* Open the `versions_local.env` in Visual Studio Code

```sh
code versions_local.env
```

* Change the values to your needs, e.g.

```sh
export REGISTRY='quay.io'
export ORG='tsuedbroecker'
export COMMON_TAG='v1.0.36'
```

* Open a terminal in the project and use the `versions_local.env` as input for your environment variables

```sh
source versions_local.env
podman login $REGISTRY
```

### Reference Information

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

### The script automation

The current script automation does following: 

> The functionality has variations depending on the script you are going to use.

1. It ensures that with two `versions.env` files the tagging for the container images works in a consistent way.
  
  *  [`versions.env`](https://github.com/IBM/operator-sample-go/blob/main/versions.env) for **golden sources**
  *  `versions_local.env` for **custom local configurations** your version of [`versions_local.env-template`](https://github.com/IBM/operator-sample-go/blob/main/versions_local.env-template)

2. It creates a temp `github tag` related to the last commit **before** the automation was started.

3. It creates various logs `script-automation.log` these files which will not be loaded to the git repo.

4. Resets the cluster environment:

    * [OLM](https://olm.operatorframework.io/) installation
    * [Cert manager](https://cert-manager.io/docs/) installation
    * [Prometheus operator](https://github.com/prometheus-operator/prometheus-operator) installation
    * Clean the installed operators

5. Creates following containers images:

    * `Database operator` related
        * [`Database-service`](https://github.com/IBM/operator-sample-go/tree/main/database-service)
        * [`operator-database`](https://github.com/IBM/operator-sample-go/tree/main/operator-database)
        * [`operator-database-backup`](https://github.com/IBM/operator-sample-go/tree/main/operator-database-backup)
        * `operator-database-bundle`, this is a container image which will be created by the operator-sdk and will be used later inside the `operator-database-catalog` which is relevant for `OLM` usage.
        * `operator-database-catalog`, this container image contains a reference to the `operator-database-bundle` and will be used in the context of `OLM`

    * `Application operator` related
        * [`simple-microservice`](https://github.com/IBM/operator-sample-go/tree/main/simple-microservice)
        * [`operator-application-autoscaler`](https://github.com/IBM/operator-sample-go/tree/main/operator-application-scaler)
        * [`operator-application`](https://github.com/IBM/operator-sample-go/tree/main/operator-application)
        * `operator-application-bundle`, that is a container image which will be created by the operator-sdk and will be used later inside the `operator-application-catalog` which is relevant for `OLM` usage.
        * `operator-application-catalog`, that container image contains a reference to the `operator-application-bundle` and will be used in the context of `OLM`

6. It uses templates to generate OLM deployment resources.  The resources in the repo under folders 'olm' are not used by the script.
    * [`operator-application` templates](https://github.com/IBM/operator-sample-go/tree/main/scripts/application-operator-templates)
    * [`operator-database` templates](https://github.com/IBM/operator-sample-go/tree/main/scripts/database-operator-templates)

7. Resets the `podman` vm if needed and starts podman.

8. It creates `role.yaml`, `role-binding.yaml`, `clusterserviceversion.yaml` and sample custom resources for the given operators, based on templates.

9. It verifies the pre-requisites.

10. Setup of the needed `bin` directory of the operator-sdk projects. (`controller-gen`,`kustomize`, `opm`,`setup-envtest`)