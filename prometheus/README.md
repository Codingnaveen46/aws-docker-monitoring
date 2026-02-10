Below is a **clean, production-grade, GitHub-ready documentation** for your entire project.
You can **copy–paste this directly into your README.md**.

I’ve structured it exactly the way real DevOps engineers document projects.

![Image](https://miro.medium.com/1%2AASMY8tCZWd8lM83u4ejoWA.png)

![Image](https://miro.medium.com/0%2AsV4qTWT8tt9Mhhmj)

---

# # **AWS Docker Monitoring Project (EC2 + Prometheus + Grafana)**

A complete end-to-end DevOps monitoring setup deployed on AWS using Docker containers.

---

# ## **📌 Project Overview**

This project deploys a Dockerized web application on an AWS EC2 instance, then implements a full monitoring stack using:

* **Docker**
* **Prometheus**
* **Node Exporter**
* **Grafana**
* **Linux server administration**
* **AWS EC2 networking**

You also troubleshoot real-world network and configuration issues, mirroring an actual production workflow.

---

# ## **🎯 Objectives**

* Deploy a containerized web server using Docker.
* Collect system-level metrics via Node Exporter.
* Scrape metrics using Prometheus.
* Visualize live dashboards using Grafana.
* Run everything on an AWS EC2 instance.
* Build foundational blocks for CI/CD (to be done next).

---

# ## **🧱 Architecture**

**Components:**

* **EC2 Instance (Ubuntu 24.04)**
* **Docker containers:**

  * Nginx (Web App)
  * Node Exporter (System metrics)
  * Prometheus (Metrics scrape & store)
  * Grafana (Visualization)
* **Security Group rules for required ports**

**Ports Used:**

| Service         | Port |
| --------------- | ---- |
| Web App (Nginx) | 80   |
| Prometheus      | 9090 |
| Node Exporter   | 9100 |
| Grafana         | 3000 |

---

# # **1️⃣ AWS EC2 Setup**

### **Steps**

1. Launch EC2 → Ubuntu 24.04 LTS
2. Enable **Auto-assign public IP**
3. Select/create key pair
4. Configure security group:

### **Inbound Rules**

| Type       | Port | Source    | Description     |
| ---------- | ---- | --------- | --------------- |
| SSH        | 22   | 0.0.0.0/0 | Login to server |
| HTTP       | 80   | 0.0.0.0/0 | Web app access  |
| Custom TCP | 9090 | 0.0.0.0/0 | Prometheus UI   |
| Custom TCP | 9100 | 0.0.0.0/0 | Node Exporter   |
| Custom TCP | 3000 | 0.0.0.0/0 | Grafana UI      |

---

# # **2️⃣ SSH Into EC2**

```bash
ssh -i "your-key.pem" ubuntu@<ec2-public-ip>
```

---

# # **3️⃣ Update Server**

```bash
sudo apt update && sudo apt upgrade -y
```

---

# # **4️⃣ Install Docker (Production Method)**

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

Verify:

```bash
docker --version
```

---

# # **5️⃣ Deploy Web Application using Docker (Nginx)**

```bash
docker run -d -p 80:80 --name webapp nginx
```

Test in browser:

```
http://<ec2-public-ip>
```

---

# # **6️⃣ Install Monitoring Stack**

## **6.1 Install Node Exporter**

```bash
docker run -d -p 9100:9100 --name node-exporter prom/node-exporter
```

Verify:

```
http://<ec2-public-ip>:9100/metrics
```

---

## **6.2 Create Prometheus Configuration**

Create folder:

```bash
mkdir ~/prometheus
cd ~/prometheus
```

Create configuration file:

```bash
nano prometheus.yml
```

Paste this (**replace with your EC2 private IP**):

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: "node-exporter"
    static_configs:
      - targets: ["<EC2-PRIVATE-IP>:9100"]
```

Find private IP:

```bash
hostname -I
```

Use the first IP (ex: `172.31.xx.xx`).

---

## **6.3 Deploy Prometheus**

```bash
docker run -d -p 9090:9090 \
  -v ~/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \
  --name prometheus prom/prometheus
```

Check targets:

```
http://<ec2-public-ip>:9090/targets
```

Target should show: **UP**

---

# ## **6.4 Deploy Grafana**

```bash
docker run -d -p 3000:3000 --name grafana grafana/grafana
```

Open:

```
http://<ec2-public-ip>:3000
```

Login:

* Username: `admin`
* Password: `admin`

---

# ## **6.5 Add Prometheus Data Source in Grafana**

**Grafana UI → Connections → Data Sources → Add → Prometheus**

Enter:

```
http://<ec2-public-ip>:9090
```

Click: **Save & Test**

---

# ## **6.6 Import Grafana Dashboard (Node Exporter Full)**

Steps:

1. Grafana → Dashboards
2. Click **Import**
3. Enter ID:

```
1860
```

4. Choose Prometheus datasource
5. Import

Dashboard now shows:

* CPU usage
* Memory consumption
* Disk usage
* Network traffic
* Node health metrics

---

# # **7️⃣ Troubleshooting Done During Setup**

During configuration, the following real-time issues were resolved:

| Issue                      | Root Cause                             | Fix                                 |
| -------------------------- | -------------------------------------- | ----------------------------------- |
| SSH timeout                | Security group missing port 22         | Added SSH rule                      |
| Browser timeout on port 80 | No HTTP rule                           | Added port 80 rule                  |
| Prometheus “DOWN” target   | Used `localhost:9100` inside container | Updated to private IP               |
| URL escape error `%20`     | Multiple IPs copied from `hostname -I` | Kept only first private IP          |
| Grafana dashboard no-data  | Prometheus target not UP               | Restarted Prometheus + fixed config |

This reflects real DevOps debugging workflows.

---

# ## **8️⃣ Running Containers Summary**

Output from `docker ps -a`:

| Container     | Image              | Port | Purpose            |
| ------------- | ------------------ | ---- | ------------------ |
| webapp        | nginx              | 80   | Web server         |
| node-exporter | prom/node-exporter | 9100 | System metrics     |
| prometheus    | prom/prometheus    | 9090 | Metrics collection |
| grafana       | grafana/grafana    | 3000 | Dashboards         |

---

# # **9️⃣ Project Outcome**

By completing this project, you have implemented:

* Cloud hosting using **AWS EC2**
* Containerization using **Docker**
* Monitoring using **Prometheus & Node Exporter**
* Visualization using **Grafana**
* Linux system administration
* AWS networking (SG, routes, IPs)
* Production-level troubleshooting

This forms a **full DevOps monitoring pipeline**.

---

# 🔟 **10️⃣ Next Phase (Coming Next)**

You will add:

## ➤ **CI/CD Pipeline using GitHub Actions**

To automatically:

* Build Docker image
* Push code changes
* Redeploy app on EC2

This will make your project **end-to-end DevOps ready**.

---

If you want, I can also generate a **professional README format**, **repo structure**, or **project explanation for your resume**.
