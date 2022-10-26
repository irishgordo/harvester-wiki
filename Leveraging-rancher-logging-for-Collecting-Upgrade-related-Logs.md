This page contains two sections:

1. How to enable rancher-logging introduced in Harvester v1.1.0 in v1.0.3
2. General configuration for collecting upgrade-related logs with rancher-logging

If you're testing the upgrade path from v1.0.3 to v1.1.0, both parts are worth referencing.

## Enabling rancher-logging in v1.0.3

> The following instructions might put the Harvester cluster into an unstable state, please do not try it on the production environment.

The main idea is to apply `rancher-logging` and `rancher-logging-crd` ManagedCharts to the v1.0.3 cluster. But before that, let's first set the image tag of the `harvester-cluster-repo` Deployment to the new one, i.e. v1.1.0:

```bash
kubectl -n cattle-system set image deploy/harvester-cluster-repo httpd=rancher/harvester-cluster-repo:v1.1.0
```

Note: you have to preload the image onto each node for the rollout to succeed. Since the `rancher/harvester-cluster-repo` is not publicly published as of now, it is essential to build one at your hand.

```bash
git clone https://github.com/harvester/harvester.git
cd harvester/
git checkout tags/v1.1.0 -b v1.1.0
make build-iso  # This might take some time since it builds almost everything including the ISO image we don't need here
docker save -o harvester-cluster-repo-master.tar rancher/harvester-cluster-repo:master
```

Transfer the archived image to each Harvester node via `scp` then load the image:

```bash
docker load -i /tmp/harvester-cluster-repo-master.tar
```

After the `harvester-cluster-repo` Pod went back online, apply the `rancher-logging` and `rancher-logging-crd` ManagedCharts with the following command:

```bash
kubectl apply -f https://raw.githubusercontent.com/harvester/harvester/v1.1.0/package/upgrade/migrations/managed_charts/logging-audit-v1.0.3.yaml
```

Please wait a moment for it to reflect the changes on the cluster. You can see the ManagedCharts are deployed and in the desired state:

```bash
$ kubectl -n fleet-local get managedcharts rancher-logging rancher-logging-crd -o json | jq '.items[].status.conditions[] | select(.type == "Ready")'
{
  "lastUpdateTime": "2022-10-26T10:48:45Z",
  "status": "True",
  "type": "Ready"
}
{
  "lastUpdateTime": "2022-10-26T10:48:33Z",
  "status": "True",
  "type": "Ready"
}
$ kubectl -n cattle-logging-system get pods
NAME                                                      READY   STATUS      RESTARTS   AGE
rancher-logging-574448c578-x668q                          1/1     Running     0          3m8s
rancher-logging-kube-audit-fluentbit-8rjhz                1/1     Running     0          2m36s
rancher-logging-kube-audit-fluentbit-mgqsm                1/1     Running     0          2m36s
rancher-logging-kube-audit-fluentbit-rfqdl                1/1     Running     0          2m36s
rancher-logging-kube-audit-fluentbit-xppsj                1/1     Running     0          2m36s
rancher-logging-kube-audit-fluentd-0                      2/2     Running     0          2m38s
rancher-logging-kube-audit-fluentd-configcheck-ac2d4553   0/1     Completed   0          2m53s
rancher-logging-rke2-journald-aggregator-q6g5p            1/1     Running     0          3m8s
rancher-logging-rke2-journald-aggregator-w2mwz            1/1     Running     0          3m8s
rancher-logging-rke2-journald-aggregator-w6dqf            1/1     Running     0          3m8s
rancher-logging-rke2-journald-aggregator-wrr46            1/1     Running     0          3m8s
rancher-logging-root-fluentbit-ddrhx                      1/1     Running     0          2m38s
rancher-logging-root-fluentbit-gvklq                      1/1     Running     0          2m38s
rancher-logging-root-fluentbit-sn4t7                      1/1     Running     0          2m38s
rancher-logging-root-fluentbit-tr7gj                      1/1     Running     0          2m38s
rancher-logging-root-fluentd-0                            2/2     Running     0          2m39s
rancher-logging-root-fluentd-configcheck-ac2d4553         0/1     Completed   0          2m52s
```

## Setting up rancher-logging for upgrade-related logs

If your Elasticsearch node(s) has basic authn & authz configured, you will need to create a Secret resource for rancher-logging to reference.

```bash
$ kubectl -n cattle-logging-system create secret generic elastic-user --from-literal=PASSWORD=password -o yaml --dry-run=client | tee -a elastic-user-secret.yaml
apiVersion: v1
data:
  PASSWORD: cGFzc3dvcmQ=
kind: Secret
metadata:
  creationTimestamp: null
  name: elastic-user
  namespace: cattle-logging-system
```

Set up a ClusterOutput CR regarding Elasticsearch so that logs associated with this ClusterOutput will be routed to the Elasticsearch node(s). In `es-co.yaml`:

```yaml
apiVersion: logging.banzaicloud.io/v1beta1
kind: ClusterOutput
metadata:
  name: es-output
  namespace: cattle-logging-system
spec:
  elasticsearch:
    hosts: "es-01.example.com:9200,es-02.example.com:9200,es-03.example.com:9200"
    ## If you have only one elasticsearch node, the following will work, too
    # host: es.example.com
    # port: 9200
    ## If the elasticsearch node(s) is set up without any credentials, the user and password can be omitted
    user: elastic
    password:
      valueFrom:
        secretKeyRef:
          key: PASSWORD
          name: elastic-user
    logstash_format: true
    logstash_prefix: fluentd
    include_timestamp: true
    suppress_type_name: true
    log_es_400_reason: true
    scheme: https
    ssl_verify: true
    ssl_version: TLSv1_2
    buffer:
      timekey: 1m
      timekey_wait: 30s
      timekey_use_utc: true
```

You can also set another ClusterOutput with the **file** output plugin. Note that the logs will be archived under the designated path **in the fluend Pod**, which means the logs are ephemeral. This is just for verifying that the desired logs are actually being processed by rancher-logging. In `file-co.yaml`:

```yaml
apiVersion: logging.banzaicloud.io/v1beta1
kind: ClusterOutput
metadata:
  name: file-output
  namespace: cattle-logging-system
spec:
  file:
    path: /tmp/logs/${tag}/%Y/%m/%d.%H.%M
    append: true
    buffer:
      timekey: 1m
      timekey_wait: 10s
      timekey_use_utc: true
```

Here we can pick the logs we care about, and route them to the specific outputs. This includes the logs generated by:

- Harvester Pods
- Rancher Pods
- Pods that do the image preloading
- Pods that do the manifest applying
- Pods that do the node upgrading

In `upgrade-related-cf.yaml`:

```yaml
apiVersion: logging.banzaicloud.io/v1beta1
kind: ClusterFlow
metadata:
  name: upgrade-related-flow
  namespace: cattle-logging-system
spec:
  filters:
  - dedot:
      de_dot_separator: "-"
      de_dot_nested: true
  match:
  # harvester pods
  - select:
      labels:
        app.kubernetes.io/name: harvester
      namespaces:
      - harvester-system
  # rancher pods
  - select:
      labels:
        app: rancher
      namespaces:
      - cattle-system
  # image preload pods
  - select:
      labels:
        upgrade.cattle.io/controller: system-upgrade-controller
      container_names:
      - upgrade
      namespaces:
      - cattle-system
  # upgrade manifest pods
  - select:
      labels:
        harvesterhci.io/upgradeComponent: manifest
      namespaces:
      - harvester-system
  # upgrade node pods
  - select:
      labels:
        harvesterhci.io/upgradeComponent: node
      namespaces:
      - harvester-system
  globalOutputRefs:
  - es-output
  - file-output
```

After that, apply the manifest files:

```bash
kubectl apply -f elastic-user-secret.yaml -f es-co.yaml -f file-co.yaml -f upgrade-related-cf.yaml
```