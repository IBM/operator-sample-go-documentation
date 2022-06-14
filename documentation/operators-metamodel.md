# 5.6 The Kubernetes Operator Metamodel


### Introduction

This sections describes the key objects and concepts you need to understand before building operators. The diagram shows the key concepts and high level architecture, based on the Kubebuilder Architecture Concept Diagram

Most of the concepts in the diagram are generic for all types of operators, no matter how they have been implemented. Some parts are specific to operators built with Golang, Operator SDK including Kubebuilder and the [Operator Lifecycle Manager Framework] (https://book.kubebuilder.io/architecture.html).

 ![Operator Metamodel](/images/OperatorMetaModel.png)

#### Operator

The term ‘Operator’ or ‘Kubernetes Operator’ describes the mechanism to automate deployments and day 2 operations for software running on Kubernetes which implements the operator pattern. This pattern is used by Kubernetes internally as well as externally for custom resources. Operators contain custom resource definitions (CRDs) and business logic to manage these resources. The self contained operators are deployed as containers on Kubernetes. Usually there is one running operator instance per cluster. For production deployments the Operator Lifecycle Manager (OLM) provides functionality to deploy and manage the operators, for example to handle multiple versions.

Examples from the Operator Sample Go project:

[Operator Dockerfile](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/Dockerfile)
[Operator CSV](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/bundle/manifests/operator-application.clusterserviceversion.yaml)
[Operator initialization in main.go](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/main.go)

#### APIs

The term API is often used as synonym for a custom resource definition. Custom resource definitions have schemas and potentially multiple versions. This allows managing resources declaratively in Kubernetes production environments. The CRD defines the schema an administrator would use to deploy the solution managed by the operator.  The operator in turn creates and manages the necessary Kubernetes resources (e.g. pods, statefulsets, PVs etc).

Custom resource definitions are identified by their group, version and resource name. One operator can define multiple CRDs.

Examples from the Operator Sample Go project:

[Sample resource](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/config/samples/application.sample_v1beta1_application.yaml)
[Schema as Go struct](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/api/v1beta1/application_types.go)
[Schema as yaml](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/config/crd/bases/application.sample.ibm.com_applications.yaml)
[Versions](https://github.com/IBM/operator-sample-go/tree/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/api)

#### Manager

The manager contains the business logic of the operator which knows how to deploy and manage custom resources. Additionally it comes with generic built in functionality to handle HA leader election, export metrics, handle webhook certs and broadcasts events to Kubernetes. It also provides a client used by the controller to access Kubernetes APIs, and a cache to improve efficiency.

Examples from the Operator Sample Go project:

[Manager creation in main.go](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/main.go#L57)


#### Controller

The main responsibility of controllers is to synchronize the ‘to be’ states as defined in custom resources with the ‘as is’ states in Kubernetes clusters. This includes creations of new resources, updates to existing resources or deletions. This logic is implemented in the controllers’ reconcile function. The reconciler doesn’t use an imperative model to manage resources because of the nature of distributed Kubernetes systems and because of the long time it can take to change resources without blocking anything. Instead the reconciler is invoked over and over again until it signals that it’s done. This is why reconcilers need to be idempotent. One controller manages one custom resource definition including all versions of it. The controller uses caches and Kubernetes clients and gets events via filters.


Examples from the Operator Sample Go project:

[Flow in Reconcile function](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/controllers/application/controller.go)
[Synchronization of resources](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/controllers/application/service.go#L43-L49)
[Creations and updates of resources](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/controllers/application/secret.go)
[Definition of resources to watch](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/controllers/application/controller.go#L126)


#### Webhooks

With webhooks values of resources can be changed and conversions between different versions can be done.

Examples from the Operator Sample Go project:

[Initialization](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/api/v1beta1/application_webhook.go#L28)
[Validation](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/api/v1beta1/application_webhook.go#L38-L83)
[Conversion](https://github.com/IBM/operator-sample-go/blob/884e14053522645fcfedde38e4ae8a03378902f8/operator-application/api/v1alpha1/application_conversion.go)
