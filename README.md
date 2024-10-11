Here’s a basic `README` template for setting up and configuring the Prometheus `Remote Write` feature for sending metrics to a remote destination:

---

# Prometheus Metric Source with Remote Write

## Overview

This repository provides a simple setup for exporting Prometheus metrics using the **Remote Write** feature. Prometheus **Remote Write** allows metrics to be pushed to remote storage backends, enabling long-term retention, advanced query capabilities, and high availability.

## Prerequisites

Before you begin, ensure you have the following installed on your system:
- Prometheus (version 2.x or higher)
- A configured remote storage backend (such as Cortex, Thanos, or VictoriaMetrics)
- Docker (optional, if using Docker-based deployment)
- Kubernetes (optional, if using Kubernetes for deployment)

## Features

- Exports Prometheus metrics using the `remote_write` API.
- Supports various remote storage backends.
- Customizable metric retention and storage policies.

## Getting Started

### Step 1: Install Prometheus

If Prometheus is not installed, follow the installation steps:

- **For Docker:**
    ```bash
    docker run -p 9090:9090 prom/prometheus
    ```

- **For Kubernetes (via Helm):**
    ```bash
    helm install prometheus prometheus-community/prometheus
    ```

### Step 2: Configure `prometheus.yml`

Modify the Prometheus configuration file (`prometheus.yml`) to include the `remote_write` section. Here's an example configuration:

```yaml
global:
  scrape_interval: 15s # How often to scrape metrics
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

remote_write:
  - url: "http://<remote-storage-url>/api/v1/write"
    queue_config:
      capacity: 5000
      max_shards: 200
      min_shards: 1
      max_samples_per_send: 1000
      batch_send_deadline: 5s
    write_relabel_configs:
      - source_labels: [__name__]
        regex: "up"
        action: keep
```

#### Parameters:
- `url`: The URL of the remote storage backend where Prometheus will send the metrics.
- `queue_config`: Configuration for handling outgoing metric samples.
  - `capacity`: Buffer size before sending data to remote storage.
  - `max_shards`: Maximum number of parallel connections to send metrics.
  - `min_shards`: Minimum number of parallel connections.
  - `max_samples_per_send`: Maximum number of samples to send per request.
  - `batch_send_deadline`: Maximum time to wait before sending a batch.
- `write_relabel_configs`: Configurations to filter which metrics are sent to the remote backend.

### Step 3: Start Prometheus

Once the configuration file has been updated, restart Prometheus to apply the changes:

- **For Docker:**
    ```bash
    docker run -p 9090:9090 -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
    ```

- **For Kubernetes (if using Helm):**
    ```bash
    helm upgrade prometheus prometheus-community/prometheus -f prometheus.yml
    ```

### Step 4: Verify Remote Write Configuration

You can check whether the metrics are being successfully sent to the remote storage backend by checking the Prometheus logs:

```bash
docker logs <prometheus-container-id>
```

Look for logs related to `remote_write` to confirm that metrics are being transmitted.

## Customization

You can fine-tune the Prometheus remote write behavior by adjusting the following settings:

- **Scrape Interval:** Adjust `scrape_interval` to increase or decrease the frequency of metric collection.
- **Relabeling:** Use the `write_relabel_configs` to filter specific metrics from being sent to the remote storage.
- **Sharding:** Control the number of parallel connections using the `min_shards` and `max_shards` parameters.

## Common Use Cases

1. **Long-term Storage:**
   Push metrics to a long-term storage system such as **Cortex**, **Thanos**, or **VictoriaMetrics** for querying historical data.

2. **Scaling Prometheus:**
   Use **Remote Write** to scale Prometheus by offloading data to an external system, reducing the load on local storage.

3. **High Availability:**
   Combine with **Thanos** or **Cortex** for a high-availability setup, ensuring metric retention during Prometheus restarts.

## Troubleshooting

1. **Metrics not reaching remote storage?**
   - Check that the `url` in the `remote_write` section is correct.
   - Verify network connectivity between Prometheus and the remote storage.
   - Check Prometheus logs for any errors related to `remote_write`.

2. **Slow ingestion performance?**
   - Increase `max_shards` or `capacity` to handle larger metric loads.
   - Verify that the remote storage backend can handle the volume of data.

## Contributing

We welcome contributions! If you find any issues or have ideas for improvement, feel free to open an issue or submit a pull request.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.

---

Feel free to adjust this `README` to fit your specific project setup or include more details as necessary.
