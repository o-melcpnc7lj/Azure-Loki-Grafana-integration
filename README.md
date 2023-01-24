# Loki-Grafana-integration
**Prerequisites:**

**i. S3 storage

**ii. ROSA/ARO/OCP 4.11 cluster

1. To start with Loki, install cluster logging operator in `openshift-logging` namespace from the operator hub
~~~
$ oc get pods -w -n openshift-logging
~~~
2. Install loki operator
~~~
$ oc get pods -w -n openshift-operators-redhat
~~~
3. Create CRD for ES
~~~
$ oc create -f logging.openshift.io_elasticsearches.yaml
~~~
4. Configure Loki

  I] Create S3 bucket in AWS
  
  II] Create Secrete for S3
~~~
$ oc project openshift-logging
$ oc create -f loki-s3-secret.yaml
~~~
5. Create LokiStack CR
~~~
$ oc create -f logging-loki.yaml
~~~
6. Create ClusterLogging CR
~~~
$ oc create -f clusterlogging.yaml
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
$ oc create -f grafana-cr-proxy-secret.yaml
$ oc create -f grafana-cr-htpasswd-secret.yaml
$ oc create -f grafana-cr-creds-secret.yaml
$ oc create -f grafana-cr.yaml 
$ oc get pods -w -n openshift-logging
~~~
11. Login to grafana console

11. Add datasources (Prometheus and loki) from browser

I] Modify the grafana-datasources.yaml file to add bearer token for grafana service account
~~~
$ oc sa get-token grafana-serviceaccount (If this doesn't work out then next step)
$ oc get sa -oyaml grafana-serviceaccount (Check the secret and copy token and replace it with $BEARER_TOKEN in prometheus datasource)
~~~
Add the role to the Grafana serviceaccount:
~~~
$ oc adm policy add-cluster-role-to-user cluster-monitoring-view -z grafana-serviceaccount
~~~
