---
title: Prometheus Monitoring
description: This tutorial explains how Prometheus monitors targets/endpoints
---

### Prometheus Monitoring


Prometheus is designed to monitor targets. Servers, databases, standalone virtual machines etc can be monitored with Prometheus.

Prometheus monitoring feature :

- Prometheus is a pull based monitoring system.

- Prometheus actively screens targets in order to retrieve metrics from them.

- In order to monitor systems, Prometheus will periodically scrap them.

- Prometheus expects to retrieve metrics via HTTP calls done to endpoints that are defined in Prometheus configuration.


Lets take the example of MariaDB Server. Here, we will explain the monitoring of MariaDB Sever using Prometheus. 

### How to monitor MariaDB server using Prometheus 

Step 1:  Install the MariaDB operator by running the following command:


```execute
kubectl create -f https://operatorhub.io/install/mariadb-operator-app.yaml             
```

- watch your operator come up using below command :


```execute
kubectl get csv -n my-mariadb-operator-app
```

- Get the associated Pods using below command:


```execute
kubectl get pods -n my-mariadb-operator-app
```


Step 2: To create MariaDB database called test-db along with user credentials , create the below yaml definition of the Custom Resource.

```execute
cat <<'EOF' > MariaDBserver.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: MariaDB
metadata:
  name: mariadb
spec:
  # Keep this parameter value unchanged.
  size: 1
  
  # Root user password
  rootpwd: password

  # New Database name
  database: test-db
  # Database additional user details (base64 encoded)
  username: db-user 
  password: db-user 

  # Image name with version
  image: "mariadb/server:10.3"

  # Database storage Path
  dataStoragePath: "/mnt/data" 

  # Database storage Size (Ex. 1Gi, 100Mi)
  dataStorageSize: "1Gi"

  # Port number exposed for Database service
  port: 30685
EOF
```

- Execute below command to create an instance of MariaDBserver using the above yaml definition:

```execute
kubectl create -f MariaDBserver.yaml -n my-mariadb-operator-app 
```

- Check pods status :

```execute
kubectl get pods -n my-mariadb-operator-app
```

Step 3 : Access MariaDB Database. 

- Connect to MariaDB Server pod.

 - Copy below command to the terminal,add the podname of MariaDB Server Instance.
    
 ```copycommand
 kubectl exec -it <podname> bash -n my-mariadb-operator-app
 ```


 - Connect to the database using username **db-user** and password **db-user**


 ```execute
 mysql -h ##DNS.ip## -P 30685 -u db-user -pdb-user
 ```


- list database

```execute
show databases;
```


- exit the database.


```execute
exit
```


- To login through root user use below command:


```
mysql -h ##DNS.ip## -P 30685 -u root -ppassword
```





Step 4: Enable monitoring service for MariaDB Server.


- Execute below command to get services of MariadB:
  
  ```execute
  kubectl get svc -n my-mariadb-operator-app
  ```

 Output:
 ```
      NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
 mariadb-operator-metrics   ClusterIP   10.111.158.91    <none>        8383/TCP,8686/TCP   3d4h
 mariadb-service            NodePort    10.106.178.202   <none>        80:30685/TCP        3d4h
 ```
 
- Get the port of mariadb-service :
  
  From above command output, mariadb-service port is 30685 

- To enable monitoring using Prometheus exporter pod and service , create the below yaml definition of the Custom Resource.

```execute
cat <<'EOF'> MariaDBmonitoring.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: Monitor
metadata:
  name: mariadb-monitor
spec:
  # Add fields here
  size: 1
  # Database source to connect with for colleting metrics
  # Format: "<db-user>:<db-password>@(<dbhost>:<dbport>)/<dbname>">
  # Make approprite changes 
  dataSourceName: "root:password@(##DNS.ip##:30685)/test-db"
  # Image name with version
  # Refer https://registry.hub.docker.com/r/prom/mysqld-exporter for more details
  image: "prom/mysqld-exporter"
EOF
```

Note: The database host and port should be correct for metrics to work. Host will be cluster IP and the port will be mariadb-service port(see Step 4).


- Execute below command to Create Instance of Monitoring 

```execute
kubectl create -f MariaDBmonitoring.yaml -n my-mariadb-operator-app
```

This will start Prometheus exporter pod and service. 



- Check pods status :

```execute
kubectl get pods -n my-mariadb-operator-app
```



Step 5: Create Instance of ServiceMonitor to monitor MariaDB Services:


```execute
cat <<'EOF' > ServiceMonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: mariadb-monitor 
  labels:
    app: playground
  namespace: operators
spec:
  namespaceSelector:
    any: true
  selector:
    matchLabels:
      tier: monitor-app 
  endpoints:
   - interval: 20s
     port: monitor    
EOF
```


- Execute below command to create ServiceMonitor instance :


```execute
kubectl create -f ServiceMonitor.yaml -n operators
```

Step 6 : Access the Prometheus dashboard using below link. 

```
http://##DNS.ip##:30100
```

On the prometheus UI, Go to Status tab. Choose option:Targets to see endpoints.

Step 7 : Go to Step of Creating Grafana Instance and Service for Grafana Instance in this tutorial.


Step 8:Create the below yaml definition of the Custom Resource to create Instance of Grafana Datasources :

```execute
cat <<'EOF' > prometheus-datasources.yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDataSource
metadata:
  name: prometheus-grafanadatasource
spec:
  datasources:
    - access: proxy
      editable: true
      isDefault: true
      jsonData:
        timeInterval: 5s
      name: Prometheus
      type: prometheus
      url: 'http://prometheus-operated.operators:9090'
      version: 1
  name: prometheus-datasources.yaml  
EOF
```


Execute below command to create an instance of Grafana datasourse using the above yaml definition::


```execute
kubectl create -f prometheus-datasources.yaml -n my-grafana-operator
```


Step 9 : Import Grafana dashboard via Grafana UI


- Execute below command to get all services in "my-grafana-operator" namespace.

 ```execute
kubectl get svc -n my-grafana-operator
```


Output :


Get the Nodeport of grafana-svc using which we can access Grafana dashboard.


- Access Grafana UI using url : [http://##DNS.ip##:30200](http://##DNS.ip##:30101/)


- Save below MariaDBDashboard.json on your local system. 

 Once we logged-in to the Grafana-UI using our credentials, We need to import this MariaDBDashboard.json file from Grafana UI to configure our Grafana Dashboard using datasourse as : Prometheus.


MariaDBDashboard.json

```
{
  "annotations": {
    "list": [
      {
        "builtIn": 1,
        "datasource": "-- Grafana --",
        "enable": true,
        "hide": true,
        "iconColor": "rgba(0, 211, 255, 1)",
        "name": "Annotations & Alerts",
        "type": "dashboard"
      }
    ]
  },
  "editable": true,
  "gnetId": null,
  "graphTooltip": 0,
  "id": 1,
  "links": [],
  "panels": [
    {
      "aliasColors": {},
      "bars": false,
      "dashLength": 10,
      "dashes": false,
      "datasource": "Prometheus",
      "fill": 1,
      "fillGradient": 0,
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 0,
        "y": 0
      },
      "hiddenSeries": false,
      "id": 4,
      "legend": {
        "avg": false,
        "current": false,
        "max": false,
        "min": false,
        "show": true,
        "total": false,
        "values": false
      },
      "lines": true,
      "linewidth": 1,
      "nullPointMode": "null",
      "options": {
        "dataLinks": []
      },
      "percentage": false,
      "pointradius": 2,
      "points": false,
      "renderer": "flot",
      "seriesOverrides": [],
      "spaceLength": 10,
      "stack": false,
      "steppedLine": false,
      "targets": [
        {
          "expr": "mysql_up",
          "format": "time_series",
          "legendFormat": "Server Status(1=UP , 0=Down)",
          "refId": "A"
        }
      ],
      "thresholds": [],
      "timeFrom": null,
      "timeRegions": [],
      "timeShift": null,
      "title": "MariaDB Server Status",
      "tooltip": {
        "shared": true,
        "sort": 0,
        "value_type": "individual"
      },
      "type": "graph",
      "xaxis": {
        "buckets": null,
        "mode": "time",
        "name": null,
        "show": true,
        "values": []
      },
      "yaxes": [
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        },
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        }
      ],
      "yaxis": {
        "align": false,
        "alignLevel": null
      }
    },
    {
      "aliasColors": {},
      "bars": false,
      "dashLength": 10,
      "dashes": false,
      "datasource": "Prometheus",
      "fill": 1,
      "fillGradient": 0,
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 0
      },
      "hiddenSeries": false,
      "id": 6,
      "legend": {
        "avg": false,
        "current": false,
        "max": false,
        "min": false,
        "show": true,
        "total": false,
        "values": false
      },
      "lines": true,
      "linewidth": 1,
      "nullPointMode": "null",
      "options": {
        "dataLinks": []
      },
      "percentage": false,
      "pointradius": 2,
      "points": false,
      "renderer": "flot",
      "seriesOverrides": [],
      "spaceLength": 10,
      "stack": false,
      "steppedLine": false,
      "targets": [
        {
          "expr": "mysql_global_status_memory_used",
          "interval": "",
          "legendFormat": "Memory Used",
          "refId": "A"
        }
      ],
      "thresholds": [],
      "timeFrom": null,
      "timeRegions": [],
      "timeShift": null,
      "title": "Memory Used",
      "tooltip": {
        "shared": true,
        "sort": 0,
        "value_type": "individual"
      },
      "type": "graph",
      "xaxis": {
        "buckets": null,
        "mode": "time",
        "name": null,
        "show": true,
        "values": []
      },
      "yaxes": [
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        },
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        }
      ],
      "yaxis": {
        "align": false,
        "alignLevel": null
      }
    },
    {
      "aliasColors": {},
      "bars": false,
      "dashLength": 10,
      "dashes": false,
      "datasource": "Prometheus",
      "fill": 1,
      "fillGradient": 0,
      "gridPos": {
        "h": 9,
        "w": 12,
        "x": 0,
        "y": 8
      },
      "hiddenSeries": false,
      "id": 2,
      "legend": {
        "avg": false,
        "current": false,
        "max": false,
        "min": false,
        "show": true,
        "total": false,
        "values": false
      },
      "lines": true,
      "linewidth": 1,
      "nullPointMode": "null",
      "options": {
        "dataLinks": []
      },
      "percentage": false,
      "pointradius": 2,
      "points": false,
      "renderer": "flot",
      "seriesOverrides": [],
      "spaceLength": 10,
      "stack": false,
      "steppedLine": false,
      "targets": [
        {
          "expr": "mysql_global_status_threads_connected",
          "legendFormat": "Active DB Connections",
          "refId": "A"
        }
      ],
      "thresholds": [],
      "timeFrom": null,
      "timeRegions": [],
      "timeShift": null,
      "title": "Active DB Connections",
      "tooltip": {
        "shared": true,
        "sort": 0,
        "value_type": "individual"
      },
      "type": "graph",
      "xaxis": {
        "buckets": null,
        "mode": "time",
        "name": null,
        "show": true,
        "values": []
      },
      "yaxes": [
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        },
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        }
      ],
      "yaxis": {
        "align": false,
        "alignLevel": null
      }
    },
    {
      "aliasColors": {},
      "bars": false,
      "dashLength": 10,
      "dashes": false,
      "datasource": "Prometheus",
      "fill": 1,
      "fillGradient": 0,
      "gridPos": {
        "h": 8,
        "w": 12,
        "x": 12,
        "y": 8
      },
      "hiddenSeries": false,
      "id": 7,
      "legend": {
        "avg": false,
        "current": false,
        "max": false,
        "min": false,
        "show": true,
        "total": false,
        "values": false
      },
      "lines": true,
      "linewidth": 1,
      "nullPointMode": "null",
      "options": {
        "dataLinks": []
      },
      "percentage": false,
      "pointradius": 2,
      "points": false,
      "renderer": "flot",
      "seriesOverrides": [],
      "spaceLength": 10,
      "stack": false,
      "steppedLine": false,
      "targets": [
        {
          "expr": "mysql_global_status_commands_total{command=\"insert\"}",
          "legendFormat": "Total DB Inserts",
          "refId": "A"
        }
      ],
      "thresholds": [],
      "timeFrom": null,
      "timeRegions": [],
      "timeShift": null,
      "title": "Total DB Inserts",
      "tooltip": {
        "shared": true,
        "sort": 0,
        "value_type": "individual"
      },
      "type": "graph",
      "xaxis": {
        "buckets": null,
        "mode": "time",
        "name": null,
        "show": true,
        "values": []
      },
      "yaxes": [
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        },
        {
          "format": "short",
          "label": null,
          "logBase": 1,
          "max": null,
          "min": null,
          "show": true
        }
      ],
      "yaxis": {
        "align": false,
        "alignLevel": null
      }
    }
  ],
  "refresh": false,
  "schemaVersion": 21,
  "style": "dark",
  "tags": [],
  "templating": {
    "list": []
  },
  "time": {
    "from": "now-6h",
    "to": "now"
  },
  "timepicker": {
    "refresh_intervals": [
      "5s",
      "10s",
      "30s",
      "1m",
      "5m",
      "15m",
      "30m",
      "1h",
      "2h",
      "1d"
    ]
  },
  "timezone": "",
  "title": "MariaDB Dashboard",
  "uid": "MariaDB",
  "version": 6
}
```




