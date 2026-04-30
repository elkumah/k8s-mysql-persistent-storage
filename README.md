# 🚀 Kubernetes MySQL StatefulSet with Persistent Storage

## 📌 Project Overview

This project demonstrates how to deploy a **MySQL database on Kubernetes using a StatefulSet** with persistent storage.

Unlike a basic Pod deployment, this setup ensures:

- **Stable pod identity**
- **Persistent storage per replica**
- **Reliable database behavior across restarts**

This is the recommended approach for running **stateful applications** like databases in Kubernetes.

---

## 🧠 Key Concepts Covered

- Kubernetes StatefulSets
- PersistentVolumeClaims (PVCs)
- Dynamic volume provisioning
- Headless Services
- Secrets for credential management
- Volume mounting (`mountPath`)
- Data persistence across pod restarts

---

## 🏗️ Architecture

```
Client
  ↓
Headless Service (mysql)
  ↓
StatefulSet (mysql-0)
  ↓
PersistentVolumeClaim (mysql-storage-mysql-0)
  ↓
PersistentVolume (PV)
  ↓
Disk Storage
```

---

## 📁 Project Structure

```
.
├── mysql-secret.yaml
├── mysql-service.yaml        # Headless Service
├── mysql-statefulset.yaml
└── README.md
```

---

## 🔐 Step 1: Create Secret

Stores the MySQL root password securely.

```bash
kubectl create secret generic mysql-secret \
  --from-literal=MYSQL_ROOT_PASSWORD=YourStrongPassword
```

> ⚠️ Note: Secrets are Base64-encoded, not encrypted. Avoid committing real credentials.

---

## 🌐 Step 2: Create Headless Service

Required for StatefulSet networking.

```bash
kubectl apply -f mysql-service.yaml
```

This service uses:

```
clusterIP: None
```

to provide stable DNS for the StatefulSet.

---

## 🐬 Step 3: Deploy MySQL StatefulSet

```bash
kubectl apply -f mysql-statefulset.yaml
```

This will:

- Create pod: `mysql-0`
- Automatically create PVC: `mysql-storage-mysql-0`

---

## 🔍 Step 4: Verify Deployment

```bash
kubectl get pods
kubectl get pvc
```

Expected output:

- Pod: `mysql-0` → `Running`
- PVC: `mysql-storage-mysql-0` → `Bound`

---

## 🔗 Step 5: Connect to MySQL

```bash
kubectl exec -it mysql-0 -- bash
mysql -u root -p
```

---

## 🧪 Step 6: Test Data Persistence

```sql
CREATE DATABASE testdb;
```

Delete the pod:

```bash
kubectl delete pod mysql-0
```

Kubernetes will recreate it automatically.

Verify:

```sql
SHOW DATABASES;
```

✅ `testdb` should still exist

---

## 🔄 Why StatefulSet Instead of Pod?

| Feature    | Pod          | StatefulSet                |
| ---------- | ------------ | -------------------------- |
| Identity   | Ephemeral    | Stable (`mysql-0`)         |
| Storage    | Manual PVC   | Auto-managed PVC           |
| Scaling    | Not suitable | Designed for stateful apps |
| Networking | Basic        | Stable DNS                 |

---

## ⚠️ Important Notes

- MySQL reads `MYSQL_ROOT_PASSWORD` **only during first initialization**
- If the PVC already contains data, updating the Secret will NOT change the password
- StatefulSet fields like `volumeClaimTemplates` are **immutable**
- Deleting the StatefulSet does **not delete PVCs** (data is preserved)

---

## 🛠️ Troubleshooting

### Access denied error

- Ensure you're using the correct password from the Secret
- If needed, reset by deleting PVC (data loss)

### Lost connection / MySQL crash

- Check logs:

  ```bash
  kubectl logs mysql-0
  ```

### PVC stuck in Pending

- Verify StorageClass:

  ```bash
  kubectl get storageclass
  ```

---

## 🚀 Improvements (Next Steps)

- Add Kubernetes **Service (ClusterIP)** for internal access
- Connect to a **Node.js backend (3-tier architecture)**
- Use **StatefulSet scaling + MySQL replication**
- Add **Helm charts**
- Deploy to cloud (AWS EKS, GKE, AKS)

---

## 🧩 Lessons Learned

- StatefulSets are essential for databases in Kubernetes
- Persistent storage must align with application data paths
- Secrets must be managed carefully
- Storage state overrides environment variables after initialization
- Debugging requires logs, describe, and understanding pod lifecycle

---

## 👨‍💻 Author

Emmanuel Fordjour

---

## 📜 License

MIT License
