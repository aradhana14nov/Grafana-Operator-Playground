<h1 align="center">Grafana Operator</h1>

![Logo](_images/logo.PNG)

A Kubernetes Operator based on the Operator SDK for creating and managing Grafana instances.

Grafana is an open platform for beautiful analytics and monitoring. For more information please visit the [Grafana website](https://grafana.com/)

# Current status

The Operator can deploy and manage a Grafana instance on Kubernetes and OpenShift. The following features are supported:

- Install Grafana to a namespace
- Configure Grafana through the custom resource
- Import Grafana dashboards from the same or other namespaces
- Import Grafana data sources from the same namespace
- Install Plugins (panels)
