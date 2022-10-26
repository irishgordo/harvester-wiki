This page contains two sections:

1. How to enable rancher-logging introduced in Harvester v1.1.0 in v1.0.3
2. General configuration for collecting upgrade-related logs with rancher-logging

If you're testing the upgrade path from v1.0.3 to v1.1.0, both parts are worth referencing.

## Enabling rancher-logging in v1.0.3

## Setting up rancher-logging for upgrade-related logs

In `elastic-user-secret.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: elastic-user
  namespace: cattle-logging-system
data:
  PASSWORD: password
```

In `es-co.yaml`:

```yaml
apiVersion: logging.banzaicloud.io/v1beta1
kind: ClusterOutput
metadata:
  name: es-output
  namespace: cattle-logging-system
spec:
  elasticsearch:
    hosts: "es-01.example.com:9200,es-02.example.com:9200,es-03.example.com:9200"
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
    scheme: https
    ssl_verify: true
    ssl_version: TLSv1_2
    log_es_400_reason: true
    buffer:
      timekey: 1m
      timekey_wait: 30s
      timekey_use_utc: true
```

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

```bash
kubectl apply -f elastic-user-secret.yaml -f es-co.yaml -f upgrade-related-cf.yaml
```