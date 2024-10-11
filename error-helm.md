@rifaterdemsahin ➜ /workspaces/PrometheusMetricSourcewithRemoteWrite (main) $ helm install prometheus prometheus-community/prometheus
Error: INSTALLATION FAILED: repo prometheus-community not found

## Solution

To resolve this issue, you need to add the `prometheus-community` repository to Helm. You can do this by running the following command:

```sh
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

After adding the repository, try installing the chart again.