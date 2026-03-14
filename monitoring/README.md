# Monitoring Stack

This directory contains the configuration for the TechITFactory monitoring stack.

## Components

| Component | Purpose | Port |
|-----------|---------|------|
| Prometheus | Metrics collection | 9090 |
| Grafana | Visualization | 3000 |
| Alertmanager | Alert routing | 9093 |
| Loki | Log aggregation | 3100 |
| Promtail | Log collection | - |

## Installation

### Via ArgoCD (Recommended)
```bash
kubectl apply -f ../apps/monitoring.yaml
```

### Manual Installation
```bash
# Create namespace
kubectl create namespace monitoring

# Install Prometheus Stack
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/kube-prometheus-stack \
  -n monitoring -f prometheus-values.yaml

# Install Loki Stack
helm repo add grafana https://grafana.github.io/helm-charts
helm install loki grafana/loki-stack \
  -n monitoring -f loki-values.yaml

# Apply alerts and dashboards
kubectl apply -f alerts/
kubectl apply -f dashboards/
```

## Access

### Grafana
```bash
kubectl port-forward svc/prometheus-grafana -n monitoring 3000:80
# Open http://localhost:3000
# Default: admin / TechITFactory123!
```

### Prometheus
```bash
kubectl port-forward svc/prometheus-kube-prometheus-prometheus -n monitoring 9090:9090
# Open http://localhost:9090
```

## Dashboards

Pre-configured dashboards are automatically loaded from the `dashboards/` directory.

## Alerts

Custom alert rules are defined in `alerts/techitfactory-alerts.yaml`:
- Pod health alerts
- HTTP error rate alerts
- Resource usage alerts

## Files

```
monitoring/
├── prometheus-values.yaml    # Prometheus + Grafana config
├── loki-values.yaml          # Loki + Promtail config
├── alerts/
│   └── techitfactory-alerts.yaml
├── dashboards/
│   └── techitfactory-dashboard.yaml
└── README.md
```
