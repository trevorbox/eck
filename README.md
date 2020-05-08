# Elasticsearch Cloud Kibana on Openshift

## Determine namespaces
```sh
export OPERATOR_NAMESPACE=elastic-system

export DEPLOY_NAMESPACE=elastic
```

## Install operators
See the [Deploy operator guide](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-openshift-deploy-the-operator.html)

```sh
helm template elastic-operator --namespace ${OPERATOR_NAMESPACE} | oc apply -f -
```

## Deploy Elasticsearch - auth disabled
```sh
oc new-project ${DEPLOY_NAMESPACE}

#Required for Operator to watch our ${DEPLOY_NAMESPACE}
oc patch statefulset/elastic-operator \
  -n ${OPERATOR_NAMESPACE} \
  --type='json' \
  --patch '[{"op":"add","path":"/spec/template/spec/containers/0/env/-","value": {"name": "NAMESPACE", "value": "'"${DEPLOY_NAMESPACE}"'"}}]'

helm template elasticsearch --namespace ${DEPLOY_NAMESPACE} | oc apply -f -
```

## Deploy Kibana with Openshift oauth proxy
```sh
helm template kibana --namespace ${DEPLOY_NAMESPACE} | oc apply -f -
```

## Deploy Heartbeat
```sh
oc adm policy add-scc-to-user privileged -z heartbeat -n ${DEPLOY_NAMESPACE}

helm template heartbeat --namespace ${DEPLOY_NAMESPACE} | oc apply -f -
```
