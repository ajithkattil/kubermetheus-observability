# Kubermetheus Observability  
> End-to-end Kubernetes observability platform (Prometheus, Alertmanager, Grafana, Loki, Promtail) with a demo app, dashboards, alerts, and logs — spin up locally in one command.
[![Kubernetes](https://img.shields.io/badge/Kubernetes-1.29+-blue?logo=kubernetes)](https://kubernetes.io/)  
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)  
[![Made with](https://img.shields.io/badge/Made%20with-❤️-red)](#)

---

## 📑 Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Plan → Build → Test → Launch](#plan--build--test--launch)
- [Quickstart](#quickstart)
- [Components](#components)
- [Demo Walkthrough](#demo-walkthrough)
- [Runbook](#runbook)
- [Cleanup](#cleanup)
- [License](#license)

---

## 📌 Overview
This repository contains a **self-contained observability platform** on Kubernetes featuring:
- **Prometheus** for metrics  
- **Alertmanager** for alerting  
- **Grafana** for dashboards  
- **Loki + Promtail** for log aggregation  
- A **demo application** with metrics, logs, dashboards, and SLO-based alerts  


---

## 🏗 Architecture
![Architecture Diagram](diagrams/architecture.png)

- **Cluster:** kind (Kubernetes-in-Docker)  
- **Ingress:** NGINX Ingress Controller  
- **Monitoring Stack:** kube-prometheus-stack (Prometheus, Alertmanager, Grafana)  
- **Logging:** Loki (single-binary) + Promtail (daemonset)  
- **Demo App:** simple service with ServiceMonitor, log scraping, dashboards, and alerts  
- **Testing:** k6 load generator + chaos pod kill  

---

## 📋 Plan → Build → Test → Launch

**Plan**  
- SLIs/SLOs defined in `demo-alerts.yaml`  
- Architecture diagram + runbook  

**Build**  
- Helm + manifests install Prometheus, Alertmanager, Grafana, Loki, Promtail  
- Grafana datasources provisioned automatically  

**Test**  
- `make test` sends synthetic load with k6  
- Chaos: delete demo pods → verify alerts in Alertmanager  
- Logs visible in Grafana Explore (via Loki)  

**Launch**  
- Access UIs via Ingress hostnames (`*.localtest.me`)  
- Optional: `make tunnel` for temporary public URLs via Cloudflare Tunnel  

---

## 🚀 Quickstart

```bash
# Requirements: Docker, kind, kubectl, helm
make up            # create cluster + monitoring stack + demo app
make pf            # open ports (optional, if not using ingress)
make test          # generate load + trigger alerts

## 🚀 Access UIs

Grafana → http://grafana.localtest.me (admin / prom-operator)

Prometheus → http://prometheus.localtest.me

Alertmanager → http://alertmanager.localtest.me

Loki → http://loki.localtest.me

👉 localtest.me resolves to 127.0.0.1 automatically.

## 🚀 Components

Prometheus: collects metrics, scrapes ServiceMonitor targets

Alertmanager: triggers alerts (infra + SLOs)

Grafana: dashboards, logs, visualizations

Loki/Promtail: logs pipeline from pods

Demo App: generates traffic, metrics, logs

k6 + Chaos: validates monitoring and alerting

## 🚀 Demo Walkthrough

Run make up → cluster + stack bootstraps.

Open Grafana → see Prometheus + Loki datasources already added.

Run make test → synthetic load + pod kill.

Prometheus: Targets → demo should be UP.

Alertmanager: DemoAvailabilityBelow99 fires.

Grafana: Explore → Loki → {namespace="demo-app"} shows pod logs.

## 🚀 Runbook

Target Down Alert → check Prometheus → find failing pod → kubectl get pods -n demo-app.

SLO Alert (<99% availability) → check Grafana dashboard → drill into Loki logs.

Remediation → kubectl -n demo-app rollout restart deploy/demo.
## 🚀 Cleanup
make down

📜 License

MIT — free to use for demos, learning, and interviews.
