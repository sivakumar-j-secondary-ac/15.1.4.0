# 15.1.4.0

## This involves no  deployments

* you should have installed helm,

* Get in to the master

* install helm

```
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > /tmp/get_helm.sh
chmod 700 /tmp/get_helm.sh
DESIRED_VERSION=v2.8.2 /tmp/get_helm.sh
helm init --wait
kubectl --namespace=kube-system create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default
helm ls
cd ~/
git clone https://github.com/kubernetes/charts
cd charts
git checkout efdcffe0b6973111ec6e5e83136ea74cdbe6527d
cd ../
```

```
cat > prometheus-values.yml
alertmanager:
    persistentVolume:
        enabled: false
server:
    persistentVolume:
        enabled: false
```

```
helm install -f prometheus-values.yml charts/stable/prometheus --name prometheus --namespace prometheus
```

```
cat > grafana-values.yml:
adminPassword: password
```

```
helm install -f grafana-values.yml charts/stable/grafana/ --name grafana --namespace grafana
```

```
cat > grafana-ext.yml
kind: Service
apiVersion: v1
metadata:
  namespace: grafana
  name: grafana-ext
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
  - protocol: TCP
    port: 3000
    nodePort: 8080
```

```
kubectl apply -f ~/grafana-ext.yml
```

```
kubectl get pods -n prometheus
kubectl get pods -n grafana
```

* Log in to grafana at <Kubernetes Node Public IP>:8081.
* Add a datasource for prometheus. The type should be set to Prometheus and the url is http://prometheus-server.prometheus.svc.cluster.local.
* Add the Kubernetes All Nodes community dashboard with id 3131.
* Create a new dashboard and add a requests per minute graph for the train-schedule app. You can use the following query:
    ```
     sum(rate(http_request_duration_ms_count[2m])) by (service, route, method, code)  * 60
  
    ```
* In the dashboard edit, add a alert and set some alert like when of ourquery is > x for last 60s. Save it
* Get in to the alerting,notification channel fill up in which way you want to receive the above alert
* All set


