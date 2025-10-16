# 🧩 Observability Stack (OpenTelemetry + Grafana Ecosystem)

This repository provides a **complete observability stack** built with Docker Compose, integrating **OpenTelemetry Collector**, **Prometheus**, **Grafana**, **Loki**, **Tempo**, and **Jaeger** — to collect, store, and visualize **metrics**, **logs**, and **traces** from your applications.

---

## 🧱 Architecture Overview

The stack follows the **OpenTelemetry pipeline** approach:

App → OpenTelemetry Collector →
├── Metrics → Prometheus → Grafana
├── Logs → Loki → Grafana
└── Traces → Tempo/Jaeger → Grafana


Each component plays a role in the three pillars of observability:

| Type | Component | Description |
|------|------------|-------------|
| **Metrics** | Prometheus | Scrapes metrics and serves them to Grafana dashboards |
| **Logs** | Loki | Centralized log aggregation |
| **Traces** | Tempo / Jaeger | Distributed tracing backends |
| **Collector** | OpenTelemetry Collector | Receives telemetry data (OTLP gRPC/HTTP) and routes it to the right backend |
| **Visualization** | Grafana | Unified UI for metrics, logs, and traces |

---

## 📦 Services

### 🧭 OpenTelemetry Collector
- **Image:** `otel/opentelemetry-collector-contrib:0.91.0`  
- **Config:** `otel-collector-config.yaml`  
- **Ports:**
  - `4317` – OTLP gRPC  
  - `4318` – OTLP HTTP  
  - `8888` – Metrics (Collector self-metrics)  
  - `13133` – Health check  
- Acts as the central telemetry hub receiving data from instrumented services.

---

### 📊 Prometheus
- **Image:** `prom/prometheus:latest`  
- **Config:** `prometheus.yml`  
- **Port:** `9090`  
- Collects and stores metrics; also acts as a data source for Grafana.

---

### 📈 Grafana
- **Image:** `grafana/grafana:latest`  
- **Port:** `3000`  
- **Default credentials:**
  - Username: `admin`
  - Password: `admin`
- **Data Sources to configure:**
  - Prometheus → `http://prometheus:9090`
  - Loki → `http://loki:3100`
  - Tempo → `http://tempo:3200`
- **Persistent storage:** `grafana-storage` volume

---

### 🪵 Loki
- **Image:** `grafana/loki:2.9.0`  
- **Port:** `3100`  
- **Config:** `loki-config.yaml`  
- Collects structured logs from applications and makes them searchable in Grafana.

---

### ⏱️ Tempo
- **Image:** `grafana/tempo:2.3.1`  
- **Config:** `tempo.yaml`  
- **Ports:**
  - `3200` – HTTP API  
  - `4317` – OTLP gRPC  
  - `4318` – OTLP HTTP  
- Stores distributed traces collected from OpenTelemetry Collector.

---

### 🔍 Jaeger
- **Image:** `jaegertracing/all-in-one:1.56`  
- **Port:** `16686`  
- UI for exploring traces. Optional — Tempo can be the main backend if preferred.

---

## 🧰 Setup & Usage

### 1️⃣ Clone the repository
```bash
git clone https://github.com/<your-username>/observability-stack.git
cd observability-stack
```

### 2️⃣ Start the stack
```bash
docker compose up -d

💡 Make sure the external Docker network shared-net already exists:
docker network create shared-net
```
## ⚙️ Configuration Files
- otel-collector-config.yaml	: Defines OTEL pipelines (receivers → processors → exporters)
- prometheus.yml	: Target endpoints for metrics scraping
- loki-config.yaml	: Loki storage and log ingestion settings
- tempo.yaml	: Tempo storage and retention config
