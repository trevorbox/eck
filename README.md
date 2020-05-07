# Elasticsearch Cloud Kibana on Openshift

## Install operators
See the [Deploy operator guide](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-openshift-deploy-the-operator.html)

```sh
oc apply -f https://download.elastic.co/downloads/eck/1.1.0/all-in-one.yaml

oc new-project elastic

#Required for Operator to watch our "elastic" namespace
oc patch statefulset/elastic-operator \
  -n elastic-system \
  --type='json' \
  --patch '[{"op":"add","path":"/spec/template/spec/containers/0/env/-","value": {"name": "NAMESPACE", "value": "elastic"}}]'
```

## Deploy Elasticsearch - auth disabled
```sh
oc create -f elasticsearch-elasticsearch-sample.yaml
```

## Deploy Kibana - auth disabled
```sh
oc create -f kibana-kibana-sample.yaml

oc create -f route-kibana-sample.yaml
```

## TODOS
- Investigate Openshift oauth sidecar and require for Kibana
- Determine how to add Heartbeat
