---
title: Grafana Instance Creation tutorial
description: This tutorial explains how create Instances for your Grafana Operator.
---

### Create this CR which will create a Grafana Instance

```execute
cat <<'EOF' > GrafanaInstance.yaml
apiVersion: integreatly.org/v1alpha1
kind: Grafana
metadata:
  name: example-grafana
spec:
  ingress:
    enabled: true
  config:
    auth:
      disable_signout_menu: true
    auth.anonymous:
      enabled: true
    log:
      level: warn
      mode: console
    security:
      admin_password: secret
      admin_user: root
  dashboardLabelSelector:
    - matchExpressions:
        - key: app
          operator: In
          values:
            - grafana
EOF
```

Execute below command to create Grafana instance

```execute
kubectl create -f GrafanaInstance.yaml -n my-grafana-operator
```
You will see the following resources to be created:

```output
grafana.integreatly.org/example-grafana created
```

Get the associated Pods:

```execute
kubectl get pods -n my-grafana-operator
```

You will be able to see the below output:

```output
NAME                                  READY   STATUS    RESTARTS   AGE
grafana-deployment-549c685ddc-b6dq7   1/1     Running   0          83s
grafana-operator-7574bbdbc9-skdk8     1/1     Running   0          6m4s
```

### Create this for Grafana Service of type NodePort

```execute
cat <<'EOF' > GrafanaService.yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-svc
spec:
  type: NodePort
  ports:
  - name: grafana
    nodePort: 30200
    port: 3000
    protocol: TCP
    targetPort: grafana-http
  selector:
    app: grafana
EOF
```

Execute below command to create Grafana Service

```execute
kubectl create -f GrafanaService.yaml -n my-grafana-operator
```
Click on the <a href="http://##DNS.ip##:30200" target="_blank">http://##DNS.ip##:30200</a> to access Grafana Dashboard from your browser.

You will see the Grafana page loading as below :
![](_images/load.png)

Now click on the `Sign In` button as below :
![](_images/signin.png)

You will now need to log in to Grafana Dashboard with the following credentials in the page below:
```
user: root
password: secret
```
![](_images/login.png)

Now you will be able to see the Dashboard like below:

![](_images/dashboard.png)


