# Overview and Scenarios

We're all familiar publishers using app stores to reach mass audiences for mobile apps.  How is this possible for enterprise applications?  The answer is via marketplaces.  Operators support the packaging of kubernetes based enterprise applications so they can be shared and promoted in various marketplaces and catalogs. This enables enterprise applications to made available digitally in a self-service fashion.

Enterprise applications are usually complex.  They are often not easy to deploy or operate and may require potential error human tasks during installation, maintenance or monitoring.

The real power or Kubernetes operators are not just day 1 tasks like the initial deployments, but also the automation of day 2 operations such as automatic backup, scaling, fault predictions, self healing, operations, reporting etc. Operators should be an integral part of the development of enterprise applications so that can be used to automate this complex tasks.

![Getting Started](./images/operators-benefits.jpeg)
<!-- ![Getting Started](http://heidloff.net/wp-content/uploads/2022/04/operators-benefits.jpg) -->

This section describes how the operators from the [Sample Operator Go](https://github.com/IBM/operator-sample-go) project automates day 2 tasks:

* The application sample operator scales up an application automatically based on the number of API requests
* The database sample operator backs up data from a simple database to cloud object storage buckets
