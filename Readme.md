
````markdown
# 🛍️ Fancy Store Web App – Google Cloud Deployment

A cloud-native deployment of the **Fancy Store** microservices e-commerce application using **Google Cloud Platform (GCP)**. This project highlights scalable architecture, load balancing, CDN, automation with startup scripts, and secure deployment with Infrastructure as Code concepts.

---

## 🚀 Overview

🧩 **Architecture Highlights:**
- Microservices: `frontend`, `orders`, and `products`
- Auto-scalable **Managed Instance Groups** (MIGs)
- **HTTP Load Balancer** with path-based routing
- **Cloud CDN** acceleration for frontend
- **Cloud Storage** for startup scripts & application code
- **Health checks** & auto-healing
- **Firewall rules** to limit exposure — no public IPs

---

## 🛠️ Setup Guide

### 1️⃣ Enable Required APIs
```bash
gcloud services enable compute.googleapis.com
gcloud services enable cloudasset.googleapis.com
````

### 2️⃣ Create a Cloud Storage Bucket

Used for startup scripts and application files.

```bash
gsutil mb gs://your-bucket-name/
gsutil cp ./scripts/startup-script.sh gs://your-bucket-name/
```

### 3️⃣ Clone Application Source Code

```bash
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
cd monolith-to-microservices/microservices
```

---

## 🧩 Compute Engine Configuration

### 4️⃣ Frontend Instance Template

```bash
gcloud compute instance-templates create fancy-fe \
  --machine-type=e2-standard-2 \
  --metadata=startup-script-url=https://storage.googleapis.com/your-bucket-name/startup-script.sh \
  --tags=frontend \
  --image-family=debian-11 --image-project=debian-cloud
```

### 5️⃣ Backend Instance Template

```bash
gcloud compute instance-templates create fancy-be \
  --machine-type=e2-standard-2 \
  --metadata=startup-script-url=https://storage.googleapis.com/your-bucket-name/backend-script.sh \
  --tags=backend \
  --image-family=debian-11 --image-project=debian-cloud
```

---

## ⚙️ Deploy Managed Instance Groups

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

## 🌍 Load Balancer Setup

### 8️⃣ Create Health Check

```bash
gcloud compute health-checks create http fancy-health-check \
  --port 80 --request-path=/healthz
```

### 9️⃣ Backend Services & URL Map

* `/` → Frontend
* `/api/orders` & `/api/products` → Backend

### 🔟 Forwarding Rule with Static IP

```bash
gcloud compute forwarding-rules create fancy-lb-forwarding-rule \
  --load-balancing-scheme=EXTERNAL \
  --port-range=80 \
  --address=<STATIC_EXTERNAL_IP> \
  --global
```

---

## 🔐 Firewall Configuration

Allow HTTP traffic only to Load Balancer:

```bash
gcloud compute firewall-rules create allow-http \
  --allow=tcp:80 \
  --target-tags=frontend,backend
```

---

## 🌐 Access & Testing

✅ Visit the app via the Load Balancer IP
✅ Backend APIs: `/api/orders` and `/api/products`
✅ No direct access to VM instances (security best practice)

---

## 🧱 Infrastructure as Code

Planning to automate? This setup can be fully scripted in **Terraform** or **Deployment Manager**.

📩 Reach out if you'd like access to the `.tf` files.

---

## 🧪 Troubleshooting Tips

* ✅ Run `gcloud config list` to confirm active project
* 🧾 Use Stackdriver Logging to debug startup script issues
* 🧠 Double-check instance group + backend service attachment
* 🔍 Use `gcloud compute instances list` to verify instance states

---

## ✍️ Author

**Omar Ibrahem**
Cloud Engineer | GCP & OCI Certified
📧 [omariibrahem24@gmail.com](mailto:omariibrahem24@gmail.com)
🔗 [LinkedIn Profile](https://www.linkedin.com/in/omar-ibrahem-687929217)
🐙 [GitHub](https://github.com/Omariibrahem)

---

> 🚧 **Future Enhancements:**
>
> * Add Terraform deployment
> * Use Cloud NAT instead of public IP for updates
> * Integrate monitoring using Cloud Monitoring & Logging

```

---
