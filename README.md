# Elasticsearch Cloud Kibana on Openshift

## Define namespaces

```sh
export OPERATOR_NAMESPACE=elastic-system
export DEPLOY_NAMESPACE=sre-monitoring
```

## Cluster Admin Tasks

This only needs to be run once by a cluster admin. All other namespaces can be controlled by a single operator namespace. See the [Deploy operator guide](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-openshift-deploy-the-operator.html)

```sh
helm template cluster-admin-tasks --namespace ${OPERATOR_NAMESPACE} | oc apply -f -
```

Whenever a new namespace wants to use the operator, run the following to tell the operator to watch the namespace.

```sh
oc patch statefulset/elastic-operator \
  -n ${OPERATOR_NAMESPACE} \
  --type='json' \
  --patch '[{"op":"add","path":"/spec/template/spec/containers/0/env/-","value": {"name": "NAMESPACE", "value": "'"${DEPLOY_NAMESPACE}"'"}}]'
```

## SRE admin tasks

This will build a new heartbeat image that can be run as a random user and push it to the internal openshift registry.

```sh
helm template heartbeat-build --namespace ${DEPLOY_NAMESPACE} | oc apply -f -
```

These should be run by an admin on the ${DEPLOY_NAMESPACE} project.

```sh
helm template sre-admin-tasks --namespace ${DEPLOY_NAMESPACE} | oc apply -f -
```
