# airgapped-kubernetes-marathon
A 3-node air-gapped Kubernetes cluster built from scratch on CentOS Stream 10, documenting 5 days of deep system troubleshooting across Linux kernel boundaries, container runtimes, and local storage layers.
## 📋 Project Overview
This repository serves as an engineering ledger documenting a 5-day infrastructure journey to bootstrap a production-equivalent, multi-node Kubernetes cluster entirely offline. 

By isolating the environment from the public internet (air-gapping), the deployment mirrors the high-security compliance standards required in enterprise environments like finance, healthcare, and telecommunications. 

The core focus of this project was navigating raw system failures—including hypervisor resource crashes, cryptographic certificate mismatches, and container runtime runtime updates—and advancing the architecture into a self-healing, stateful microservices environment.
