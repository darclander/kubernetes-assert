# Basic Kubernetes monitoring

... as Kustomize base.
The scope here is to set up basic monitoring using only `kubectl` as tooling.

## Apply

Assuming that Prometheus Operator is already installed,
start from the example kustomize base:

```
kubectl apply -k example-small
kubectl -n monitoring create -k kubernetes-mixin-dashboards
kubectl apply -k grafana
```

Note how [Prometheus](./example-small/now-prometheus.yaml) will match rules and monitors
using the label(s) that the [kustomization.yaml](./example-small/kustomization.yaml) adds.

## Re-generate stuff

This repo needs to have some generated content, where upstream kustomize bases could not be found

```
docker-compose -f docker-compose.test.yml up --build kubernetes-mixin
```

## Storage classes

Per cluster, for example

```
kubectl -n monitoring apply -f storageclasses-gke/
```

## CI test suite

```
docker volume rm kubernetes-monitoring_admin 2> /dev/null || true
./test.sh
```

## Development

```
compose='docker-compose -f docker-compose.test.yml -f docker-compose.dev-overrides.yml'
$compose down \
  ;docker volume rm kubernetes-monitoring_admin kubernetes-monitoring_k3s-server 2>/dev/null || true
$compose up -d agent
export KUBECONFIG=$PWD/test/.kube/kubeconfig.yaml
```
