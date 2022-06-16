# Overview and Scenarios

Operators are a powerful technology to automate operations of software in Kubernetes clusters. This article describes scenarios when operators should be used and which value they provide.

Kubernetes operators allow the automation of day 2 operational tasks. A good example is the automatic archiving of data. This article describes how data from a simple database can be archived automatically in S3 buckets using CronJobs, Jobs and custom resources.

![Getting Started](./images/operators-benefits.jpeg)
<!-- ![Getting Started](http://heidloff.net/wp-content/uploads/2022/04/operators-benefits.jpg) -->

The complete source code from this article is available in the [ibm/operator-sample-go](https://github.com/IBM/operator-sample-go) repo. The repo comes with a simple implementation of a database system which can be deployed on Kubernetes. Data is persisted in JSON files as outlined in the previous article Building Databases on Kubernetes with Quarkus.

The real power or Kubernetes operators are not day 1 tasks like the initial deployments, but the automation of day 2 operations. This article describes a sample operator that scales up an application automatically based on the number of API requests.