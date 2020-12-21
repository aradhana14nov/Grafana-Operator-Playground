<h1 align="center">Grafana Operator</h1>

![Logo](_images/logo.PNG)


What Is Grafana?
Grafana is a database analysis and monitoring tool. Grafana is an open platform for beautiful analytics and monitoring. It allows you to create dashboard visualizations of key metrics that are important to you. 
Grafana supports a huge number of data sources.The most common use case of Grafana is displaying time series data, such as memory or CPU over time, alongside the current usage data.

Grafana runs as a process on your computer or server, and you access the interface through your browser. Your dashboard can display your data as single numbers, graphs, charts, or even a heat map.

# Current status

The Operator can deploy and manage a Grafana instance on Kubernetes and OpenShift. The following features are supported:

- Install Grafana to a namespace
- Configure Grafana through the custom resource
- Import Grafana dashboards from the same or other namespaces
- Import Grafana data sources from the same namespace
- Install Plugins (panels)
