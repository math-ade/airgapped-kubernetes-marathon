====================================================================================================
🔒 VIRTUALBOX PRIVATE NETWORK BOUNDARY: ISOLATED HOST-ONLY SUBNET (192.168.56.0/24)
====================================================================================================

      ┌────────────────────────────────────────────────────────────────────────┐
      │  node1: CONTROL PLANE MASTER TERMINAL (192.168.56.50)                   │
      │                                                                        │
      │  ┌─────────────────────────┐  Manual Handshake Handing  ┌───────────┐  │
      │  │   kube-apiserver v1.31  │ ─────────────────────────> │  RBAC     │  │
      │  └─────────────────────────┘  (Token Management Rules)  │  Admin    │  │
      │               │                                         └───────────┘  │
      │               │ 🔐 Hand-Synced CA Signatures (ca.crt / apiserver.crt)   │
                      ▼                                                        
      └───────────────┼────────────────────────────────────────────────────────┘
                      │
                      ├──────────────────────────────────────┐
                      ▼                                      ▼
      ┌──────────────────────────────┐       ┌──────────────────────────────┐
      │ node2: WORKER 1 (192.168.56) │       │ node3: WORKER 2 (192.168.56) │
      │                              │       │                              │
      │  ┌────────────────────────┐  │       │  ┌────────────────────────┐  │
      │  │  containerd runtime    │  │       │  │  containerd runtime    │  │
      │  │  (SystemdCgroup=true)  │  │       │  │  (SystemdCgroup=true)  │  │
      │  └────────────────────────┘  │       │  └────────────────────────┘  │
      │               │              │       │               │              │
      │               ▼              │       │               ▼              │
      │  ┌────────────────────────┐  │       │  ┌────────────────────────┐  │
      │  │  Pre-Cached Sandbox    │  │       │  │  Pre-Cached Sandbox    │  │
      │  │  (registry.k8s.io)     │  │       │  │  (registry.k8s.io)     │  │
      │  └────────────────────────┘  │       │  └────────────────────────┘  │
      └──────────────────────────────┘       └──────────────────────────────┘

====================================================================================================
🌐 WORKLOAD ORCHESTRATION & DEPLOYMENT LAYER (RUNNING LIVE ON STABLE ENVIRONMENT)
====================================================================================================

               [ Inbound User Request ] ───> NodePort Gateway (Port 32000 / 30713)
                                                       │
                                                       ▼
                                      ┌─────────────────────────────────┐
                                      │  Kubernetes Load-Balancer SVC   │
                                      └─────────────────────────────────┘
                                                       │
                           ┌───────────────────────────┼───────────────────────────┐
                           ▼                           ▼                           ▼
              ┌─────────────────────────┐ ┌─────────────────────────┐ ┌─────────────────────────┐
              │   Pod 1: NGINX Web      │ │   Pod 2: NGINX Web      │ │   Pod 3: NGINX Web      │
              │  Status: 1/1 Running    │ │  Status: 1/1 Running    │ │  Status: 1/1 Running    │
              │                         │ │                         │ │                         │
              │ 🛠️ LivenessProbe Active  │ │ 🛠️ LivenessProbe Active  │ │ 🛠️ LivenessProbe Active  │
              │ 📦 ConfigMap Mounted     │ │ 📦 ConfigMap Mounted     │ │ 📦 ConfigMap Mounted     │
              └─────────────────────────┘ └─────────────────────────┘ └─────────────────────────┘
                                                       │
                                                       ▼
                                      ┌─────────────────────────────────┐
                                      │   PersistentVolumeClaim (PVC)   │
                                      └─────────────────────────────────┘
                                                       │
                                                       ▼
                                      ┌─────────────────────────────────┐
                                      │    PostgreSQL Database Pod      │
                                      │    Status: [BOUND STORAGE]      │
                                      └─────────────────────────────────┘


# 🚀 3-Node Air-Gapped Kubernetes Cluster & Microservices Deployment

This project documents 5 days of intense engineering troubleshooting to deploy a multi-node Kubernetes cluster entirely offline (air-gapped) on CentOS Stream 10.

---

## 🛠️ The 5-Day Debugging Ledger

### 1. Linux Kernel Soft Lockups (Hardware Freezes)
- **Problem:** The system froze and printed `kernel:watchdog: BUG: soft lockup - CPU stuck for 92s!`.
- **Fix:** Cleared the frozen memory files, turned off swap memory allocations, and increased the virtual machine RAM sizes to keep the services alive.

### 2. Security Certificate Mismatches
- **Problem:** The terminal threw errors saying `tls: failed to verify certificate: x509: certificate is valid for 192.168.1.52, not 192.168.56.50`.
- **Fix:** Deleted the broken certificates, forced Kubernetes to regenerate fresh keys for the correct IP address, and copied them securely across the private network.

### 3. Container Runtime Failures
- **Problem:** Worker nodes failed to join and timed out waiting for the internet to pull setup files.
- **Fix:** Rewrote the container config file to use the local machine driver and pointed the background settings to local backup files, bypassing the internet entirely.

---

## 🌐 Advanced Cluster Features Implemented
Once the network and hardware were completely stable, I successfully configured:
- **Load Balancing:** Exposed application containers to the outside world using network NodePort objects.
- **Dynamic Content:** Used ConfigMaps to dynamically rewrite and mount custom website files inside live containers with zero downtime.
- **Self-Healing Architecture:** Configured Liveness and Readiness probes so the cluster automatically heals itself if an app crashes.
- **Persistent Storage:** Connected a PostgreSQL database to a PersistentVolumeClaim to keep data safe across system reboots.

