# Real-Time Delivery Monitoring Pipeline

## **Objective**
You are a DevOps engineer in ZIPPTO managing a fast-paced delivery service that needs real-time insights into operational efficiency and delivery performance.
You task is to create monitoring application top provide insights on performance of the delivery service.

## **Approach**
Using Python, Prometheus, Grafana and Jenkins, create a pipeline that simulates delivery metrics, visualizes them on dashboards and sets up automated alerts to ensure smooth operations and quick problem detection.

---

## **1. Prerequisites**
Ensure the following tools are installed:
- **Docker**: For containerizing and running services.
- **Python**: Version 3.10 or higher.
- **Jenkins**: For pipeline automation.
- **Prometheus**: For metrics scraping.
- **Grafana**: For data visualization.

---

## **2. Code Directory Structure**

```
delivery_monitoring/
├── delivery_metrics.py   # Python script to simulate metrics
├── prometheus.yml        # Prometheus configuration
├── Jenkinsfile           # Jenkins pipeline script
```

---

## **3. Steps to Set Up and Simulate**

### **Step 1: Write the Python Application**
Create a file named `delivery_metrics.py`:

```python
from prometheus_client import start_http_server, Summary, Gauge
import random
import time

# Metrics
total_deliveries = Gauge("total_deliveries", "Total number of deliveries")
pending_deliveries = Gauge("pending_deliveries", "Number of pending deliveries")
on_the_way_deliveries = Gauge("on_the_way_deliveries", "Number of deliveries on the way")
average_delivery_time = Summary("average_delivery_time", "Average delivery time in seconds")

# Simulate delivery statuses
def simulate_delivery():
    pending = random.randint(10, 50)
    on_the_way = random.randint(5, 20)
    delivered = random.randint(30, 70)
    avg_time = random.uniform(15, 45)

    total = pending + on_the_way + delivered

    total_deliveries.set(total)
    pending_deliveries.set(pending)
    on_the_way_deliveries.set(on_the_way)
    average_delivery_time.observe(avg_time)

if __name__ == "__main__":
    start_http_server(8000)
    while True:
        simulate_delivery()
        time.sleep(5)
```

Run the script:
```bash
python delivery_metrics.py
```
Access metrics at [http://localhost:8000/metrics](http://localhost:8000/metrics).

---

### **Step 2: Configure Prometheus**

Create a file named `prometheus.yml`:

```yaml
scrape_configs:
  - job_name: "delivery_service"
    static_configs:
      - targets: ["host.docker.internal:8000"]
```

Run Prometheus:
```bash
docker run -d --name prometheus -p 9090:9090 \
  -v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```
Access Prometheus at [http://localhost:9090](http://localhost:9090).

---

### **Step 3: Set Up Grafana**

Run Grafana:
```bash
docker run -d --name grafana -p 3000:3000 grafana/grafana
```

Access Grafana at [http://localhost:3000](http://localhost:3000).

1. Add Prometheus as a data source:
   - URL: `http://host.docker.internal:9090`.
2. Create a new dashboard with the following panels:
   - **Panel 1**: Query `total_deliveries`.
   - **Panel 2**: Query `pending_deliveries` and `on_the_way_deliveries`.
   - **Panel 3**: Query `average_delivery_time`.

---

### **Step 4: Automate Alerts in Prometheus**

Update `prometheus.yml`:

```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets: ["localhost:9093"]

alerts:
  - alert: HighPendingDeliveries
    expr: pending_deliveries > 40
    for: 5m
    labels:
      severity: critical
    annotations:
      summary: "High Pending Deliveries"
      description: "Pending deliveries exceeded threshold: {{ $value }}."
```

Restart Prometheus:
```bash
docker restart prometheus
```
Verify alerts in the Prometheus UI under the "Alerts" tab.

---

### **Step 5: Create the Jenkins Pipeline**

Create a file named `Jenkinsfile`:

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-repo/delivery_monitoring.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t delivery_metrics .'
            }
        }
        stage('Run Application') {
            steps {
                sh 'docker run -d -p 8000:8000 --name delivery_metrics delivery_metrics'
            }
        }
        stage('Run Prometheus & Grafana') {
            steps {
                sh '''
                docker run -d --name prometheus -p 9090:9090 \
                  -v $WORKSPACE/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
                docker run -d --name grafana -p 3000:3000 grafana/grafana
                '''
            }
        }
    }
}
```

Run Jenkins:
```bash
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

Access Jenkins at [http://localhost:8080](http://localhost:8080) and create a pipeline job using the `Jenkinsfile`.

---

### **Step 6: Simulate Alerts**

Modify `delivery_metrics.py` to increase `pending_deliveries` values:

```python
pending = random.randint(50, 100)  # Increase range to exceed alert threshold
```

Restart the Python script and Prometheus. Verify alerts in the Prometheus UI and Grafana.

---

## **Expected Outputs**
1. **Metrics Endpoint:** [http://localhost:8000/metrics](http://localhost:8000/metrics).
2. **Prometheus Query:** Graphs for `total_deliveries`, `pending_deliveries`, and `average_delivery_time`.
3. **Grafana Dashboard:** Visualizations for delivery metrics and trends.
4. **Jenkins Logs:** Successful pipeline build with logs for Docker commands.
5. **Prometheus Alerts:** Critical alerts for high pending deliveries.

---

Feel free to reach out for further assistance!
