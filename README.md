# 🚀 Kubernetes MySQL Deployment with Persistent Storage

## 📌 Project Overview

This project demonstrates how to deploy a **MySQL database on Kubernetes** with **persistent storage** using a PersistentVolumeClaim (PVC).

It ensures that database data **survives pod restarts**, which is critical for stateful applications.

---

## 🧠 Key Concepts Covered

- Kubernetes Pods
- Persistent Volumes (PV) & Persistent Volume Claims (PVC)
- Secrets for secure credential management
- Volume mounting (`mountPath`)
- Containerized MySQL deployment
- Data persistence in Kubernetes

---

## 🏗️ Architecture

```
MySQL Pod
   ↓
PersistentVolumeClaim (PVC)
   ↓
PersistentVolume (PV)
   ↓
Storage (Disk)
```

---

## 📁 Project Structure

```
.
├── mysql-secret.yaml
├── mysql-pvc.yaml
├── mysql-pod.yaml
└── README.md
```

---

## 🔐 Step 1: Create Secret

Stores the MySQL root password securely.

```bash
echo -n 'password' | base64
```

Apply:

```bash
kubectl apply -f mysql-secret.yaml
```

---

## 💾 Step 2: Create PersistentVolumeClaim

Requests persistent storage for MySQL data.

```bash
kubectl apply -f mysql-pvc.yaml
```

---

## 🐬 Step 3: Deploy MySQL Pod

```bash
kubectl apply -f mysql-pod.yaml
```

---

## 🔍 Step 4: Verify Deployment

```bash
kubectl get pods
kubectl get pvc
```

Expected:

- Pod status: `Running`
- PVC status: `Bound`

---

## 🔗 Step 5: Connect to MySQL

```bash
kubectl exec -it mysql -- bash
mysql -u root -p
```

---

## 🧪 Step 6: Test Persistence

```sql
CREATE DATABASE testdb;
```

Delete pod:

```bash
kubectl delete pod mysql
```

Recreate happens automatically. Then verify:

```sql
SHOW DATABASES;
```

✅ `testdb` should still exist

---

## ⚠️ Important Notes

- MySQL reads `MYSQL_ROOT_PASSWORD` **only during first initialization**
- If PVC already contains data, the password will not be updated
- Use `mountPath: /var/lib/mysql` for proper data persistence

---

## 🧩 Lessons Learned

- Persistent storage is essential for databases in Kubernetes
- PVCs retain data beyond pod lifecycle
- Misconfigured storage can lead to data loss or initialization issues
- Debugging involves logs, describe, and understanding container lifecycle

---

## 👨‍💻 Author

**Emmanuel Fordjour Kumah**

---

## 📜 License

This project is open-source and available under the MIT License.
