# Elasticsearch Cloud Kibana on Openshift

Docs: [Elastic Cloud on Kubernetes](https://www.elastic.co/guide/en/cloud-on-k8s/1.2/k8s-overview.html)

Github: [elastic/cloud-on-k8s](https://github.com/elastic/cloud-on-k8s)

## Define namespaces

```sh
export DEPLOY_NAMESPACE=sre-monitoring
oc new-project ${DEPLOY_NAMESPACE}
```

## Cluster Admin Tasks

```sh
oc adm policy add-cluster-role-to-group system:auth-delegator system:serviceaccounts:${DEPLOY_NAMESPACE} --rolebinding-name=oauth-proxy-serviceaccounts

helm upgrade -i cluster-admin-tasks -n ${DEPLOY_NAMESPACE} cluster-admin-tasks
```

### Elastic Cloud ECK Operators

```sh
helm upgrade -i elastic-cloud-eck-operators -n ${DEPLOY_NAMESPACE} elastic-cloud-eck-operators
```

> Note: you need to manually approve the InstallPlan - run the command below and follow the URL

```sh
echo "https://$(oc get route console -o jsonpath={.spec.host} -n openshift-console)/k8s/ns/${DEPLOY_NAMESPACE}/operators.coreos.com~v1alpha1~InstallPlan"
```

## SRE admin tasks

This will build a new heartbeat image that can be run as a random user and push it to the internal openshift registry.

```sh
helm upgrade -i heartbeat-build -n ${DEPLOY_NAMESPACE} heartbeat-build
```

The following needs to be run by a developer on the ${DEPLOY_NAMESPACE} project.

```sh
helm upgrade -i heartbeat -n ${DEPLOY_NAMESPACE} sre-admin-tasks
```



docker.elastic.co/eck/eck-operator:1.2.1
docker.elastic.co/elasticsearch/elasticsearch:7.6.2
docker.elastic.co/kibana/kibana:7.6.2
image-registry.openshift-image-registry.svc:5000/sre-monitoring5/heartbeat-build:latest
quay.io/openshift/origin-oauth-proxy:4.6


argocd app create eck-cluster-admin-tasks --repo https://github.com/trevorbox/eck.git --path cluster-admin-tasks --dest-server https://kubernetes.default.svc --dest-namespace sre-monitoring5 --sync-policy automated --upsert --revision master

argocd app create eck-elastic-cloud-eck-operators --repo https://github.com/trevorbox/eck.git --path elastic-cloud-eck-operators --dest-server https://kubernetes.default.svc --dest-namespace sre-monitoring5 --sync-policy automated --upsert --revision master

argocd app create eck-heartbeat-build --repo https://github.com/trevorbox/eck.git --path heartbeat-build --dest-server https://kubernetes.default.svc --dest-namespace sre-monitoring5 --sync-policy automated --upsert --revision master

argocd app create eck-sre-admin-tasks --repo https://github.com/trevorbox/eck.git --path sre-admin-tasks --dest-server https://kubernetes.default.svc --dest-namespace sre-monitoring5 --sync-policy automated --upsert --revision master