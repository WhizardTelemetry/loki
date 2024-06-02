## Prereq
```shell
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add minio https://charts.min.io/
helm repo update
helm dependency build ./loki
```

## Install Loki
```shell
helm upgrade --install --force -n loki --create-namespace -f whizard-loki/simple-scalable-values.yaml loki ./loki
```

## Uninstall Loki
```shell
helm uninstall -n loki loki
kubectl -n loki delete pvc data-loki-backend-0 data-loki-backend-1 data-loki-write-0 data-loki-write-1 export-0-loki-minio-0 export-1-loki-minio-0
kubectl -n kubesphere-logging-system delete secrets vector-agent-logs-sink-loki
```

## Install and config Grafana

```shell
helm upgrade --install -n grafana --create-namespace grafana grafana/grafana
kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

### Config Loki data source in grafana
- Set `URL` to `http://loki-gateway.loki.svc` or other loki gateway address
- Add HTTP Header `X-Scope-OrgID` and set the value to a tenant key like `host1|member1`