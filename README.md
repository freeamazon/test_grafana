Monitoring Stack (Prometheus + Grafana + Loki + Alertmanager + Promtail)
This repository contains a self-hosted monitoring and logging stack built using Docker Compose. It is designed to monitor local and remote servers, collect system metrics, application logs (including Koha), and send email alerts for critical conditions.

📦 Components Included
Component	Purpose
Prometheus	Metrics collection & alert evaluation
Alertmanager	Alert routing & email notifications
Grafana	Dashboards, visualization & UI-based alerting
Loki	Log aggregation backend
Promtail	Log shipping agent (system, nginx, apache, koha)
Node Exporter	System-level metrics (CPU, RAM, Disk, Load)

⚠️ Note: Runtime data directories are generated automatically. Do not manually edit files inside them.

🚀 How to Run
1️⃣ Prerequisites
    • Linux system (Ubuntu recommended)
    • Docker >= 20.x
    • Docker Compose v2
    • Open ports: 3000, 9090, 9093, 3100, 9100
Verify:
docker --version
docker compose version

2️⃣ Clone Repository

3️⃣ Create Required Directories
mkdir -p prometheus-data loki-data alertmanager-data promtail-data grafana-storage
Set permissions:
sudo chown -R 472:472 grafana-storage
sudo chown -R 10001:10001 loki-data
sudo chmod -R 755 grafana-storage loki-data prometheus-data alertmanager-data promtail-data

4️⃣ Start the Stack
docker compose up -d
Check status:
docker ps

🌐 Access URLs
Service	URL
Grafana	http://localhost:3000
Prometheus	http://localhost:9090
Alertmanager	http://localhost:9093
Loki	http://localhost:3100
Grafana Login: - User: admin - Password: admin

📊 Metrics Monitoring
Node Exporter
    • Runs on host using network_mode: host
    • Exposes metrics on port 9100
Prometheus jobs: - node – local & remote servers - prometheus - alertmanager - loki - promtail
Verify targets:
http://localhost:9090/targets

📜 Log Collection (Promtail)
Promtail collects logs from: - /var/log/**/*.log - /var/log/nginx/*.log - /var/log/apache2/*.log - /var/log/koha/**/*.log
Extracted labels: - ip - method - path - status - size
View logs in Grafana:
Explore → Loki

🚨 Alerting
Prometheus Alerts
Defined in:
config/prometheus/rules/alerts.yml
Includes: - Instance Down - High CPU / Load / Memory / Disk - Promtail Down - Loki Down - HTTP 4xx / 5xx errors

Alertmanager (Email)
Configured in:
config/alertmanager/alertmanager.yml
Behavior: - Group wait: 10s - Group interval: 1m - Repeat interval: 10m - Sends resolved alerts

📩 Email Notifications
Uses Gmail SMTP (App Password).
Required fields:
smtp_smarthost: smtp.gmail.com:587
smtp_auth_username: your_email@gmail.com
smtp_auth_password: <gmail_app_password>

🖥️ Remote Server Monitoring
Metrics
    • Install node-exporter on remote server
    • Add IP to prometheus.yml
Example:
- targets: ['103.102.234.16:9100']
  labels:
    name: test-instance2
Logs
    • Remote server must run promtail to send logs
    • Loki alone does NOT push logs

🧠 Hostname vs Instance
    • instance = IP:PORT
    • nodename = hostname (from node_uname_info)
Grafana variable query used:
label_values(node_uname_info, nodename)

🧹 Common Issues & Fixes
❌ Grafana restarting
Cause: permission issue on /var/lib/grafana
Fix:
sudo chown -R 472:472 grafana-storage

❌ No data in dashboards
Possible causes: - Time drift between servers - Old Prometheus data after config change - Remote target down
Fix:
rm -rf prometheus-data/*
docker compose restart prometheus

✅ Summary
✔ Local & remote monitoring ✔ Log aggregation (Koha-ready) ✔ Email alerting ✔ Dockerized & portable ✔ Production-ready structure

Maintained by Aditya                               

