# Example script invocations

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
````

To build all images, push to a registry and deploy to Kubernetes:
```
sh scripts/ci-create-operators-kubernetes.sh app local reset podman_reset
````


### Reference Information

There are four types of installation scripts:

* 1. **install-required**
* 2. **demo**
* 3. **ci**
* 4. **delete**

#### 1. **install-required**-xxx-components.sh

* Definition: 
`xxx == Kubernetes or OpenShift`

* Table overview:

Installs the required components for Kubernetes or OpenShift.

| Component | Kubernetes | OpenShift |
| --- |  --- |  --- |
| CertManager | Yes |  Yes |
| OLM | Yes |  No |
| Prometheus Operator | Yes |  No |
| Prometheus Instance | Yes |  Only configuration |

#### 2. **demo**-xxx-yyyy.sh

Setup or delete based on the **golden source versions** (version.env).

* Definition: 

`yyy == Kubernetes or OpenShift`
`xxx == delete or setup`

* Table overview:

| Name | Kubernetes | OpenShift | Parameters |
| --- |  --- |  --- |  --- |
| **demo**-kubernetes-operators.sh | Yes | No  |  `app` `demo` `reset` |
| **demo**-kubernetes-operator-application.sh | Yes |  No  |  `demo` `reset`  |
| **demo**-kubernetes-operator-database.sh | Yes |  No  | `demo`  |
| **demo**-opershift-operators.sh | No |  Yes  | `app` `demo` `reset` |
| **demo**-opershift-operator-application.sh | No | Yes | `demo` `reset`  |
| **demo**-opershift-operator-database.sh | No | Yes  |  `demo`  |

* Setup demo for Kubernetes

```sh
sh scripts/demo-kubernetes-operators.sh app demo reset 
```

* Setup demo for OpenShift

```sh
sh scripts/demo-openshift-operators.sh app demo reset 
```

#### 3. **ci**-www-xxx-yyy-zzz.sh

* Definition: 

Creates all operators or specific operators of the project in Kubernetes or OpenShift.

* `www == create or delete`
* `xxx == operator or operators`
* `zzz == Kubernetes or OpenShift`

* Table overview:

Here is a list of available ci scripts.

| Name | Kubernetes | OpenShift | Creates Database Operator | Creates Application Operator|
| --- |  --- |  --- |  --- |  --- |
| **ci**-create-operator-database-**kubernetes**.sh | Yes | No  | Yes  | No  |
| **ci**-create-operator-application-**kubernetes**.sh | Yes |  No | No  | Yes  |
| **ci**-create-operators-**kubernetes**.sh | Yes |  No | Yes  | Yes  |
| **ci**-create-operator-database-**openshift**.sh |No | Yes  | Yes  | No  |
| **ci**-create-operator-application-**openshift**.sh | No |  Yes | No  | Yes  |
| **ci**-create-operators-**openshift**.sh | No | Yes | Yes  | Yes  |


#### 4. **delete-everything**-xxx

Deletes all depending on the Platfrom such as the operators, OLM, Prometheus or Cert-Manager.

* `xxx == Kubernetes or OpenShift`


### Script parameters

> 🔴 IMPORTANT: The order of the parameters is hard coded!

The ci-create-operators-kubernetes.sh & ci-create-operators-openshift.sh scripts supports the following parameter options. 

| Parameter combination | versions.env  |  versions_local.env | delete all and setup prerequisites | creates `operator database` | creates `operator application` | reset podman |
| --- |  --- | --- | --- |  --- | --- | --- |
| `database` `local` |  no | yes | no | yes | no | no |
| `database` `local` `reset` |  no | yes |yes | yes | no | no |
| `database` `local` `reset` `podman_reset` |  no | yes |yes | yes | no | yes |
| `app` `local` |  no | yes | no | yes | yes | no |
| `app` `local` `reset` |  no | yes |yes | yes | yes | no |
| `app` `local` `reset` `podman_reset` |  no | yes |yes | yes | yes | yes |


* First parameter: ('database' or 'app')

    * Use 'database' for setup the database operator only
    * Use 'app' for setup the database and application operator.

* Second parameter: ('local' or 'ci')

    * Use 'local' for using the `versions_local.env` file as input for the container tags.

    * Use 'ci' for using the `versions.env` file as input for the container tags. **ONLY FOR GOLDEN SOURCE!**

* Third parameter: ('reset')

    * Use 'reset' to deinstall the operators and prereq and reinstall them.

* Fourth parameter: ('podman_reset')

    * Use 'podman_reset' to delete podman vm, create a new podman vm with size of 15, and start podman.