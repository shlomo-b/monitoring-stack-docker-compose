# Monitoring Stack Docker Compose

A complete monitoring solution using Docker Compose with Prometheus, Grafana, Node-Exporter, cAdvisor, Uptime-Kuma, Loki, and Promtail.

## Overview

This repository contains a Docker Compose setup for a comprehensive monitoring stack. It includes:

- **Prometheus**: For metrics collection and storage
- **Grafana**: For metrics visualization and dashboards
- **Node-Exporter**: For host system metrics
- **cAdvisor**: For container metrics
- **Uptime-Kuma**: For uptime monitoring
- **Loki**: For log aggregation
- **Promtail**: For log collection

## Prerequisites

- Docker and Docker Compose installed
- Git

## Installation

1. Clone the repository:
   ```
   git clone https://github.com/shlomo-b/monitoring-stack-docker-compose.git
   cd monitoring-stack-docker-compose
   ```

2. (Optional) Create the secrets directory and authentication files:
   ```
   mkdir -p secrets
   echo "your_username" > secrets/prometheus_username.txt
   echo "your_password" > secrets/prometheus_password.txt
   ```

3. Start the monitoring stack:
   ```
   docker-compose -f docker-compose.yaml up -d
   ```

## Configuration

### Prometheus

Prometheus is configured using the `prometheus.yml` file. The default configuration includes:
- Basic authentication using credentials from mounted secret files
- Scrape configurations for itself and other services

(Optional) To update the Prometheus credentials:
1. Modify the username and password in the secrets files:
   ```
   echo "new_username" > secrets/prometheus_username.txt
   echo "new_password" > secrets/prometheus_password.txt
   ```

2. Restart the entire monitoring stack:
   ```
   docker-compose down
   docker-compose -f docker-compose.yaml up -d
   ```

### Grafana

Grafana is accessible at `http://localhost:3000` (default credentials: admin/admin)

To add Prometheus as a data source in Grafana:
1. Navigate to Configuration > Data Sources
2. Click "Add data source"
3. Select "Prometheus"
4. Set the URL to `http://prometheus:9090`
5. If authentication is configured, provide the username and password
6. Click "Save & Test"

### Loki & Promtail

Loki serves as the log aggregation system, while Promtail collects and forwards logs to Loki.

To configure Loki as a data source in Grafana:
1. Navigate to Configuration > Data Sources
2. Click "Add data source"
3. Select "Loki"
4. Set the URL to `http://loki:3100`
5. Click "Save & Test"

### Uptime-Kuma

Uptime-Kuma is accessible at `http://localhost:3001`. You'll need to create an initial admin account on first access.

## Architecture

The monitoring stack is organized as follows:
- Prometheus scrapes metrics from itself, Node-Exporter, and cAdvisor
- Grafana visualizes metrics from Prometheus and logs from Loki
- Node-Exporter exports host metrics to Prometheus
- cAdvisor exports container metrics to Prometheus
- Loki aggregates logs
- Promtail collects logs and sends them to Loki
- Uptime-Kuma monitors external services

## File Structure

- `docker-compose.yaml`: Main compose file to orchestrate all services
- `prometheus.yml`: Prometheus configuration
- `alertmanager.yml`: AlertManager configuration
- `loki-config.yaml`: Loki configuration
- `promtail-config.yaml`: Promtail configuration
- `secrets/`: Directory containing authentication credentials

## Security

This project uses basic authentication for Prometheus. Make sure to:

1. Change default passwords before deploying to production
2. Use strong, unique passwords
3. Restrict access to the secrets directory
4. Consider using Docker secrets or a more robust secret management solution for production

## Troubleshooting

### Common Issues

1. **Services failing to start**: Check Docker logs with `docker-compose logs [service_name]`
2. **Can't access Prometheus/Grafana**: Verify port mappings in docker-compose.yaml
3. **Authentication issues**: Ensure the credentials in secret files are properly formatted (no extra spaces)
