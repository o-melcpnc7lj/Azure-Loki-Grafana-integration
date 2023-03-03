# Loki-Grafana Integration
**Prerequisites:**

**i. Azure BloB storage**

**ii. ARO/OCP 4.11 cluster**

1. To start with Loki, install cluster logging operator in `openshift-logging` namespace from the operator hub
~~~
$ oc get pods -w -n openshift-logging
~~~
2. Install loki operator
~~~
$ oc get pods -w -n openshift-operators-redhat
~~~

4. Configure Loki

  I] Create storage account and Azure Blob in Azure
  
  II] Create Secrete for azure storage account
~~~
$ oc project openshift-logging
$ oc create -f config/loki-azure-secret.yaml
~~~
5. Create LokiStack CR
~~~
$ oc create -f config/logging-loki.yaml
~~~
6. Create ClusterLogging CR
~~~
$ oc create -f config/clusterlogging.yaml
~~~
7. Verify all the pods are running
~~~
$ oc get pods -n openshift-logging
~~~
8. Verify the logs from Console (Refresh Webconsole --> Observe --> Logs) 

9. Install grafana operator
~~~
$ oc get pods -n openshift-logging -w
~~~
10. Configure grafana
~~~
$ oc create -f config/grafana-cr-proxy-secret.yaml
$ oc create -f config/grafana-cr-htpasswd-secret.yaml
$ oc create -f config/grafana-cr-creds-secret.yaml
$ oc create -f config/grafana-cr.yaml 
$ oc get pods -w -n openshift-logging
~~~
11. Login to grafana console

11. Add datasources (Prometheus and loki) from browser

I] Modify the grafana-datasources.yaml file to add bearer token for grafana service account
~~~
$ oc sa get-token grafana-serviceaccount (If this doesn't work for some reason, extract the token from the secret as demonstrated in the next step)
$ oc get sa -oyaml grafana-serviceaccount (Check the secret and copy token and replace it with $BEARER_TOKEN in prometheus datasource)
~~~
Add the role to the Grafana serviceaccount:
~~~
$ oc adm policy add-cluster-role-to-user cluster-monitoring-view -z grafana-serviceaccount
~~~
