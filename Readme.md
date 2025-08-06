## 🛍️ Fancy Store Web App – Google Cloud Deployment

Welcome to the Fancy Store repository! This web app is deployed on Google Cloud Platform using Compute Engine instances and other GCP services to ensure scalability, security, and performance.

---

### 🚀 Overview

**Fancy Store** is a modern e-commerce web application designed with modular microservices for frontend and backend. The infrastructure uses:

- Google Compute Engine VMs
- Instance Templates
- Managed Instance Groups (MIGs)
- Load Balancer with CDN
- Cloud Storage for code & scripts
- Firewall Rules
- Health Checks & Autohealing
- Startup script automation


---

### 🧱 Architecture Components

| Component | Description |
|----------|-------------|
| **Frontend MIG** | Serves the web UI, auto-scalable & autohealed |
| **Backend MIG** | Handles business logic and database access |
| **Load Balancer** | Path-based routing (`/api/` → backend, `/` → frontend) |
| **CDN** | Cache frontend assets for faster delivery |
| **Cloud Storage** | Hosts startup scripts and source code |
| **Instance Templates** | Used to standardize frontend/backend VM creation |
| **Firewall Rules** | Allow TCP traffic on HTTP/HTTPS ports |
| **Health Checks** | Monitor VM health for autohealing |
| **Startup Scripts** | Bootstrap servers on launch |


---

### ⚙️ Setup Instructions

#### 1. Clone the Repository
```bash
git clone https://github.com/your-org/fancy-store.git
cd fancy-store
```

#### 2. Upload Assets to Cloud Storage
```bash
gsutil cp ./scripts/startup-script.sh gs://your-bucket/
```

#### 3. Create Instance Templates
Frontend:
```bash
gcloud compute instance-templates create fancy-fe \
  --machine-type=e2-standard-2 \
  --metadata=startup-script-url=https://storage.googleapis.com/your-bucket/startup-script.sh \
  --tags=frontend \
  --image-family=debian-11 --image-project=debian-cloud
```

Backend:
```bash
gcloud compute instance-templates create fancy-be \
  --machine-type=e2-standard-2 \
  --metadata=startup-script-url=https://storage.googleapis.com/your-bucket/backend-script.sh \
  --tags=backend \
  --image-family=debian-11 --image-project=debian-cloud
```

#### 4. Create MIGs
```bash
gcloud compute instance-groups managed create fancy-frontend-group \
  --base-instance-name=fancy-fe \
  --template=fancy-fe \
  --size=2 --zone=us-central1-a

gcloud compute instance-groups managed create fancy-backend-group \
  --base-instance-name=fancy-be \
  --template=fancy-be \
  --size=2 --zone=us-central1-a
```

#### 5. Create Health Checks
```bash
gcloud compute health-checks create http fancy-health-check \
  --port 80 --request-path=/healthz
```

#### 6. Setup Load Balancer & CDN (skip details here—can generate full config if needed)


---

### 🧪 Testing & Validation

- Use `curl`, `Postman`, or browser to test frontend endpoint
- Validate Load Balancer routing: `/ → frontend`, `/api → backend`
- Simulate instance failure to verify autohealing

---

### 🌐 Useful GCP Commands

- Describe template:
  ```bash
  gcloud compute instance-templates describe fancy-fe --format=json
  ```
- View Load Balancer config:
  ```bash
  gcloud compute forwarding-rules list
  ```

---

### 🔒 Security Notes

- Only expose necessary ports
- Validate startup scripts
- Use GCP IAM for access control


