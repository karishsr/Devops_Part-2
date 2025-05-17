# Part 2: Simulate IAM with MinIO

## Overview

This part simulates basic IAM-like behavior in Kubernetes using MinIO (a self-hosted S3-compatible object store). We isolate access such that:

- `data-service` has access to the S3 bucket
- `auth-service` is restricted (even if misconfigured)


## Setup Instructions

1. **Deploy MinIO** using the official Helm chart or Kubernetes manifests.

2. **Create Kubernetes Secret** for credentials:
   ```bash
   kubectl create secret generic minio-credentials \
     --from-literal=MINIO_ACCESS_KEY=minioadmin \
     --from-literal=MINIO_SECRET_KEY=minioadmin123

**Architecture Diagram**
![image](https://github.com/user-attachments/assets/274cc783-c64a-46db-b2f5-50e5de30cec7)

**Design Decisions**
Used minio/minio in standalone mode for simplicity and faster setup.

Created a temporary pod to run mc commands so that I could directly interact with the MinIO instance from within the cluster.

Chose to simulate IAM using bucket-level policies and Kubernetes-level isolation, avoiding MinIO users for simplicity.

Verified policies through direct access testing using two different service pods: data-pod and auth-pod.

**Security Incident & Fix**
Incident:
The auth-pod was mistakenly able to reach MinIO due to lack of network isolation.

Fix:
I verified the IAM simulation by ensuring only data-pod had credentials and connectivity to access the bucket, while auth-pod was restricted using a combination of:

Not mounting secrets

Verifying access denial through actual request testing

**Assumptions / Known Issues**
This is a simulation. True IAM would require object-based or user-based policy enforcement which wasn't fully implemented.

OPA/Kyverno not used at this stage (bonus task not completed).

Not using TLS for MinIO in this simulation.


