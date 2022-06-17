# Automation with scripts

Automation scripts are the recommended way to deploy the sample operators.  The scripts provide the following functions:

* Verify workstation prerequisites
* Install prerequisite components to a Kubernetes or OpenShift cluster
* Create a 'demo environment' which deploys working golden images to a Kubernetes or OpenShift cluster
* Continous integration deployment which builds all operators & applications, pushes to a registry and deploys the images using OLM to either Kubernetes or OpenShift


### Required Setup

#### a) Verification of the prerequisites

You can run to verify your workstation prerequisites with [the following script](https://github.com/IBM/operator-sample-go/blob/main/scripts/check-prerequisites.sh). The script informs you if the tools are installed, but you need to verify the versions in the terminal output with the [verified versions](./automation-version-references.md).  

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


### Script automation process

The script automation does following: 

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