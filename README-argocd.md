# ArgoCD Info

## Images

- docker.elastic.co/eck/eck-operator:1.2.1
- docker.elastic.co/elasticsearch/elasticsearch:7.6.2
- docker.elastic.co/kibana/kibana:7.6.2
- image-registry.openshift-image-registry.svc:5000/sre-monitoring5/heartbeat-build:latest
- quay.io/openshift/origin-oauth-proxy:4.6

## ArgoCD Deploy

```sh
argocd app create eck-cluster-admin-tasks --repo https://github.com/trevorbox/eck.git --path cluster-admin-tasks --dest-server https://kubernetes.default.svc --dest-namespace sre-monitoring5 --sync-policy automated --upsert --revision master

argocd app create eck-elastic-cloud-eck-operators --repo https://github.com/trevorbox/eck.git --path elastic-cloud-eck-operators --dest-server https://kubernetes.default.svc --dest-namespace sre-monitoring5 --sync-policy automated --upsert --revision master

argocd app create eck-heartbeat-build --repo https://github.com/trevorbox/eck.git --path heartbeat-build --dest-server https://kubernetes.default.svc --dest-namespace sre-monitoring5 --sync-policy automated --upsert --revision master

argocd app create eck-sre-admin-tasks --repo https://github.com/trevorbox/eck.git --path sre-admin-tasks --dest-server https://kubernetes.default.svc --dest-namespace sre-monitoring5 --sync-policy automated --upsert --revision master
```
