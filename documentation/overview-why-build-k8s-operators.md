# Why you should build Kubernetes Operators

There are multiple technologies to install and operate software in Kubernetes: kubectl, oc, Helm, Kustomize, CI/CD, GitOps, operators and more. As always the answer to the question when to use what is: It depends. Followings are the top three reasons why to use operators.

### Top three Reasons to use Operators

1. Automation of Day 2 Operations
    * Autopilot and Self-Healing
    * Predictive analytics
2. Reusability of Software
    * Hubs and Marketplaces
    * Internal and external multi-tenancy Applications
3. Leverage of the Kubernetes Community
    * Ecosystem, Tools and Offerings
    * Industry Standard


### 1) Automation of Day 2 Operations

The main purpose of operators is to automate operations. While this sounds obvious, often there is a perception that operators are only another way to deploy software. The key value of operators is to make operations of software as seamless as possible. People often think of operators as the next best way after Software as as Service (SaaS) to easily manage software running in our own clusters.

Kubernetes operators are capable to automate the expensive and error likely human operations. Features like autopilot and self-healing are great scenarios. For example databases can be archived automatically, software can automatically be scaled up and down and missing resources can be recreated when they have been deleted by mistake or in catastrophic events. It’s even possible to predict failures before they happen and to take appropriate actions.

These automations work for all types of workloads (stateful and stateless) and all types of Kubernetes resources (compute, storage, network). All business specific automation functionality can be bundled in one operator component rather than using a diverse set of tools.

### 2) Reusability of Software

Another benefit of operators is the reusability of software. With operators software and libraries can be bundled so that it can be used in different contexts. This approach is different from CI/CD pipelines where software is deployed more frequently specifically for one application.

Software providers can expose their operators in various catalogs, hubs and marketplaces like OperatorHub.io, Red Hat Marketplace, the integrated OperatorHub in OpenShift, etc. This allows them to reach new markets and to promote their software. While catalogs like OperatorHub.io are primarily used to increase awareness, marketplaces like Red Hat Marketplace provide commercialization options.

Additionally operators can be utilized to share software within companies and to share software with specific clients. In this scenario operators can be published in internal catalogs or they can be directly deployed to clusters.

### 3) Leverage of the Kubernetes Community

Kubernetes is the de-facto standard how to run software in the cloud. There is a huge ecosystem supporting Kubernetes, providing samples, tools and commercial offerings and more. Operators leverage this community, since they are a natural and Kubernetes-native way to extend Kubernetes. Operators are not only used to manage third-party resources, but they are also used internally. Because operators base on the Kubernetes model, existing development tools, CLIs, monitoring tools, etc. can be used.

Kubernetes provides proven and established industry standards how to define, deploy and operate software. For example for production workloads a declarative approach is employed (e.g. GitOps ready), custom resources can be defined and standard CLIs and data formats (yaml, json) are used. It is not necessary to learn and utilize proprietary functionality.