---
title: "NGINX shutdown strategy"
date: "2025-04-17"
description: "Make sure your nginx shuts down accordingly to Kubernetes expectations."
previewImage: "cover.png"
---

# High-SLA NGINX Shutdown Strategy on Kubernetes

![alt text](https://raw.githubusercontent.com/Murasaki-Labs/blog_articles_static/refs/heads/main/articles/nginx-shutdown-strategy/cover.png)

## 🎯 Goal

Ensure **zero-downtime** and **no client connection rejections** during Pod shutdown,
even under Service downscales, upgrades, or cluster disruptions.

We aim to:

- Allow NGINX to continue serving **existing** and **new** connections during preStop delay.
- Gracefully close active connections on shutdown.
- Avoid rejecting incoming requests during Pod termination.
- Comply with 99.99%+ SLA standards.

---

## 🔥 Action Points (What To Modify)

### 1. Modify Pod Definition

Add a `preStop` hook that **only sleeps**, allowing time for Kubernetes to update Service endpoints and kube-proxy to drain traffic:

`yaml
lifecycle:
  preStop:
    exec:
      command: ["/bin/sh", "-c", "sleep 5"]
`

Also set a proper `terminationGracePeriodSeconds` to allow full graceful shutdown:

`yaml
terminationGracePeriodSeconds: 30
`

(You can adjust `sleep 5` and `terminationGracePeriodSeconds` based on your app behavior.)

---

### 2. Modify nginx.conf

Tune NGINX to **gracefully shutdown** active connections after receiving SIGTERM:

`nginx
worker_processes auto;
events {
    worker_connections 4096;
    multi_accept on;
    use epoll;
}
http {
    worker_shutdown_timeout 5s;
}
`

This ensures that after termination starts, NGINX gives clients a short window (5s) to finish their ongoing HTTP requests before forcefully closing them.

---

## ⚙️ Full Termination Sequence (What Happens Step-by-Step)

1. **Pod Deletion Requested** (`kubectl delete pod` or ReplicaSet downscale).
2. **Control Plane**:
   - Marks Pod with `deletionTimestamp`.
   - Immediately removes Pod from **Service Endpoints**.
3. **kube-proxy** (on all nodes):
   - Updates iptables/IPVS rules.
   - Stops sending new traffic to the terminating Pod.
4. **Kubernetes**:
   - Triggers **preStop hook** in the container.
5. **Container (NGINX)**:
   - Runs `/bin/sh -c "sleep 5"`.
   - Continues to accept and serve **new** and **existing** client connections during sleep.
6. **After sleep completes**:
   - Kubernetes sends **SIGTERM** to the container's main process (NGINX master).
7. **NGINX on SIGTERM**:
   - Stops accepting new connections.
   - Continues processing existing in-flight requests.
   - Shuts down workers gracefully within `worker_shutdown_timeout` (e.g., 5 seconds).
8. **Kubernetes**:
   - Waits up to `terminationGracePeriodSeconds` for the container to exit cleanly.
   - If NGINX does not exit in time, sends **SIGKILL** to force termination (rare if settings are correct).

---

## ✅ Why This Strategy Works

- **Prevents rejecting connections** during pod shutdown.
- **Allows kube-proxy and cloud LoadBalancers** to drain traffic cleanly.
- **Gives in-flight requests time** to finish.
- **Controls maximum shutdown time** to avoid stuck pods.
- **Minimizes downtime** and meets **high SLA expectations**.

---
