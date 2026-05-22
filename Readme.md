# Kubernetes Projects 🚀

A hands-on project-based repository for engineers who already understand Kubernetes fundamentals (CKA level or equivalent) and want to build **real deployment intuition through practice**.

This repository is designed to bridge the gap between:

- Knowing Kubernetes concepts for exams
- Actually building, debugging, and operating workloads in Kubernetes

Instead of tutorials, you'll work through progressively harder projects that simulate real-world platform engineering challenges.

---

# Who This Is For

This repository is for engineers who already know:

- Pods
- Deployments
- Services
- ConfigMaps / Secrets
- Volumes / PVCs
- StatefulSets
- Ingress
- kubectl basics

If you’ve completed:

- CKA preparation
- Kubernetes fundamentals courses
- Basic cluster administration labs

…and still feel like:

> *“I understand the concepts, but I’m not yet confident building real Kubernetes projects from scratch.”*

This repo is for you.

---

# Project Roadmap

Projects are organized by difficulty level and designed to progressively build real Kubernetes engineering skills.

---

# 🟢 Beginner Level

Focus: Core Kubernetes workload deployment, networking, storage, and application architecture.

### ✅ [Project 01 - Multi-Tier Todo App](./01%20-%20Beginner%20Level/Project-01.md)

Deploy and productionize a complete 3-tier application:

- React frontend
- FastAPI backend
- PostgreSQL database
- Persistent storage
- Secrets & ConfigMaps
- Ingress routing
- Health probes
- Resource management
- Failure testing

Starter workload:

[workloads/3-tier-app](./workloads/3-tier-app)

Learn how real applications are structured inside Kubernetes.

---

### 🚧 [Project 02 - Coming Soon](./01%20-%20Beginner%20Level/Project-02.md)

Stay tuned.

---

### 🚧 [Project 03 - Coming Soon](./01%20-%20Beginner%20Level/Project-03.md)

Stay tuned.

---

# 🟡 Intermediate Level

Focus: Scaling, resiliency, observability, production architecture patterns.

### 🚧 [Project 01 - Coming Soon](./02%20-%20Intermediate%20Level/Project-01.md)

Stay tuned.

---

### 🚧 [Project 02 - Coming Soon](./02%20-%20Intermediate%20Level/Project-02.md)

Stay tuned.

---

### 🚧 [Project 03 - Coming Soon](./02%20-%20Intermediate%20Level/Project-03.md)

Stay tuned.

---

# 🔴 Advanced Level

Focus: Platform engineering, GitOps, multi-environment systems, production-grade Kubernetes.

### 🚧 [Project 01 - Coming Soon](./03%20-%20Advanced%20Level/Project-01.md)

Stay tuned.

---

### 🚧 [Project 02 - Coming Soon](./03%20-%20Advanced%20Level/Project-02.md)

Stay tuned.

---

### 🚧 [Project 03 - Coming Soon](./03%20-%20Advanced%20Level/Project-03.md)

Stay tuned.
---

# Workloads

Starter applications used by projects live here:

```bash
workloads/
```

These workloads are intentionally incomplete.

Your mission in each challenge is to:

- Containerize apps
- Design Kubernetes manifests
- Secure configuration
- Add persistence
- Expose workloads correctly
- Debug failures
- Validate production readiness

---

# How to Use This Repo

For each project:

### 1. Read the challenge

Open the project README inside its level folder.

Example:

```bash
beginner/project-01-3-tier-todo.md
```

---

### 2. Move to the starter workload

Example:

```bash
cd workloads/3-tier-app
```

---

### 3. Solve it yourself

Do not open solutions immediately.

The goal is to struggle *just enough* to build real intuition.

---

### 4. Reveal solutions only if stuck

Use solutions to compare architecture decisions, not to copy-paste blindly.

---

# Philosophy

This repo is built around one belief:

**You only truly learn Kubernetes when things break and you fix them yourself.**

Not by memorizing YAML.

Not by passing exams.

By deploying, debugging, breaking, and rebuilding systems repeatedly.

---

# Roadmap

Upcoming project themes:

**Beginner**

- Stateless web deployments
- Background workers
- Stateful databases

**Intermediate**

- Autoscaling systems
- Observability stacks
- Messaging systems

**Advanced**

- GitOps pipelines
- Service mesh architecture
- Multi-cluster platform engineering
- Production-grade security hardening

---

# Contributions

Suggestions and project ideas are welcome via Issues and Pull Requests on:

---

Built for engineers who want to move from:

**“I know Kubernetes”**

to

**“I can run Kubernetes workloads confidently in production.”**