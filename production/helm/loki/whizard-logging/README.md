helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Install Grafana
helm upgrade --install -n grafana --create-namespace grafana grafana/grafana
kubectl get secret --namespace grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

# Install Loki
helm upgrade --install --force -n loki --create-namespace -f whizard-logging/values.yaml -f whizard-logging/simple-scalable-values.yaml loki grafana/loki

# Uninstall Loki
helm uninstall -n loki loki
kubectl -n loki delete pvc data-loki-backend-0 data-loki-backend-1 data-loki-write-0 data-loki-write-1 export-0-loki-minio-0 export-1-loki-minio-0

kubectl -n kubesphere-logging-system delete secrets vector-agent-logs-sink-loki
