
```markdown
# 🛍️ Fancy Store Web App – Google Cloud Deployment

This project showcases the deployment of the **Fancy Store** e-commerce web application using **Google Cloud Platform (GCP)** services. It implements scalable architecture using Compute Engine, Cloud Storage, Managed Instance Groups, Load Balancing, and Infrastructure as Code principles.

---

## 🚀 Project Overview

Fancy Store is built with microservices: frontend, orders, and products. The infrastructure delivers:

- Auto-scalable VM instances for frontend and backend
- Load balancer with path-based routing
- CDN acceleration for frontend
- Cloud Storage for code and startup scripts
- Health checks and autohealing
- Secure configuration with firewall rules and no public IPs

---

## 🧱 Architecture Diagram

📌 Use this Eraser prompt to visualize the architecture:

```
Draw a detailed architecture diagram for hosting the "Fancy Store" web app on Google Cloud using Compute Engine. Include: Cloud Storage for source code and startup scripts, Compute Engine instances for frontend and backend microservices, Instance Templates, Managed Instance Groups with autohealing and autoscaling, HTTP(S) Load Balancer with path-based routing, CDN for frontend caching, and firewall rules for port access. Visualize health checks and startup script automation. Show public and internal IP flow between components.
```

---

## ⚙️ Step-by-Step Setup Instructions

### 1️⃣ Enable Required APIs
```bash
gcloud services enable compute.googleapis.com
gcloud services enable cloudasset.googleapis.com
```

### 2️⃣ Create Cloud Storage Bucket
Used to store startup scripts and application code.
```bash
gsutil mb gs://your-bucket-name/
gsutil cp ./scripts/startup-script.sh gs://your-bucket-name/
```

### 3️⃣ Clone the Source Code
```bash
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
cd monolith-to-microservices/microservices
```

---

## 🧩 VM & Instance Configuration

### 4️⃣ Create Frontend Instance Template
```bash
gcloud compute instance-templates create fancy-fe \
  --machine-type=e2-standard-2 \
  --metadata=startup-script-url=https://storage.googleapis.com/your-bucket-name/startup-script.sh \
  --tags=frontend \
  --image-family=debian-11 --image-project=debian-cloud
```

### 5️⃣ Create Backend Instance Template
```bash
gcloud compute instance-templates create fancy-be \
  --machine-type=e2-standard-2 \
  --metadata=startup-script-url=https://storage.googleapis.com/your-bucket-name/backend-script.sh \
  --tags=backend \
  --image-family=debian-11 --image-project=debian-cloud
```

---

## 🚀 Deploy Managed Instance Groups

### 6️⃣ Frontend MIG
```bash
gcloud compute instance-groups managed create fancy-frontend-group \
  --base-instance-name=fancy-fe \
  --template=fancy-fe \
  --size=2 --zone=us-central1-a
```

### 7️⃣ Backend MIG
```bash
gcloud compute instance-groups managed create fancy-backend-group \
  --base-instance-name=fancy-be \
  --template=fancy-be \
  --size=2 --zone=us-central1-a
```

---

## 🌐 Load Balancer & Health Checks

### 8️⃣ Create Health Check
```bash
gcloud compute health-checks create http fancy-health-check \
  --port 80 --request-path=/healthz
```

### 9️⃣ Create Backend Services & Attach MIGs
Attach the managed instance groups to the load balancer backend services with health checks.

### 🔟 Configure URL Map for Routing
- `/` → Frontend
- `/api/orders` and `/api/products` → Backend

### 1️⃣1️⃣ Create Forwarding Rule
This gives the system a public IP:
```bash
gcloud compute forwarding-rules create fancy-lb-forwarding-rule \
  --load-balancing-scheme=EXTERNAL \
  --port-range=80 \
  --address=<STATIC_EXTERNAL_IP> \
  --global
```

---

## 🔒 Firewall Rules

Allow HTTP(S) traffic only to Load Balancer:
```bash
gcloud compute firewall-rules create allow-http \
  --allow=tcp:80 \
  --target-tags=frontend,backend
```

---

## 📌 Access & Testing

- Access the app via the external IP of the **load balancer**
- Use port 8080 for frontend preview if running locally
- Backend services exposed at `/api/orders` and `/api/products`
- No public IPs for individual VMs — secure and isolated

---

## 📐 Infrastructure as Code

You can recreate this setup using Terraform for portability and versioning. Let me know if you want the `.tf` files generated and structured by service!

---

## 🧪 Troubleshooting Tips

- Check `gcloud config list` for active project and region
- Verify instance group attachment to backend service
- Validate startup script logs via Stackdriver Logging
- Use `gcloud compute instances list` to inspect VM status

---

## ✍️ Author

**Omar Ibrahem**  
Cloud Engineer 
[GitHub Profile](https://github.com/Omariibrahem)

---

