# Kubernetes Projekte 🚀

Ein praxisorientiertes Projekt-Repository für Engineers, die bereits Kubernetes-Grundlagen verstehen (CKA-Level oder vergleichbar) und **echtes Deployment-Verständnis durch praktische Übungen** aufbauen möchten.

Dieses Repository wurde entwickelt, um die Lücke zu schließen zwischen:

- dem Verständnis von Kubernetes-Konzepten für Prüfungen
- und dem tatsächlichen Bauen, Debuggen und Betreiben von Workloads in Kubernetes

Statt Tutorials bearbeitest du schrittweise anspruchsvollere Projekte, die reale Platform-Engineering-Szenarien simulieren.

---

# Für wen dieses Repository ist

Dieses Repository richtet sich an Engineers, die bereits folgende Kubernetes-Konzepte kennen:

- Pods
- Deployments
- Services
- ConfigMaps / Secrets
- Volumes / PVCs
- StatefulSets
- Ingress
- kubectl Grundlagen

Wenn du bereits Folgendes abgeschlossen hast:

- CKA Vorbereitung
- Kubernetes Grundlagenkurse
- erste Cluster-Administration-Labs

…und trotzdem das Gefühl hast:

> *„Ich verstehe die Konzepte, aber ich bin noch nicht sicher genug, um echte Kubernetes-Projekte selbstständig aufzubauen.“*

Dann ist dieses Repository für dich.

---

# Projekt-Roadmap

Die Projekte sind nach Schwierigkeitsgrad organisiert und darauf ausgelegt, schrittweise echte Kubernetes-Engineering-Skills aufzubauen.

---

# 🟢 Beginner Level

Fokus: Kubernetes Workloads, Networking, Storage und Applikationsarchitektur.

### ✅ [Projekt 01 - Multi-Tier Todo App](./01%20-%20Beginner%20Level/Project-01.md)

Deploye und produktiviere eine vollständige 3-Tier-Anwendung:

- React Frontend
- FastAPI Backend
- PostgreSQL Datenbank
- Persistenter Speicher
- Secrets & ConfigMaps
- Ingress Routing
- Health Probes
- Resource Management
- Failure Testing

Starter Workload:

👉 [3-Tier App](./workloads/3-tier-app)

---

### 🚧 [Projekt 02 - Coming Soon](./01%20-%20Beginner%20Level/Project-02.md)

Bleibt dran.

---

### 🚧 [Projekt 03 - Coming Soon](./01%20-%20Beginner%20Level/Project-03.md)

Bleibt dran.

---

# 🟡 Intermediate Level

Fokus: Skalierung, Resilienz, Observability und produktionsnahe Muster.

### 🚧 [Projekt 01 - Coming Soon](./02%20-%20Intermediate%20Level/Project-01.md)

Bleibt dran.

### 🚧 [Projekt 02 - Coming Soon](./02%20-%20Intermediate%20Level/Project-02.md)

Bleibt dran.

### 🚧 [Projekt 03 - Coming Soon](./02%20-%20Intermediate%20Level/Project-03.md)

Bleibt dran.

---

# 🔴 Advanced Level

Fokus: Plattform-Engineering, GitOps, Multi-Environment-Systeme und produktionsreife Kubernetes-Architekturen.

### 🚧 [Projekt 01 - Coming Soon](./03%20-%20Advanced%20Level/Project-01.md)

Bleibt dran.

### 🚧 [Projekt 02 - Coming Soon](./03%20-%20Advanced%20Level/Project-02.md)

Bleibt dran.

### 🚧 [Projekt 03 - Coming Soon](./03%20-%20Advanced%20Level/Project-03.md)

Bleibt dran.

---

# 📦 Workloads

Starter-Anwendungen, die in den Projekten verwendet werden, befinden sich hier:

👉 [workloads](./workloads)

Diese Workloads sind absichtlich unvollständig.

Deine Aufgabe ist es:

- Anwendungen zu containerisieren
- Kubernetes Manifeste zu erstellen
- Konfigurationen abzusichern
- Persistenz hinzuzufügen
- Workloads korrekt zu exposen
- Fehler zu debuggen
- Produktionsreife zu validieren

---

# 🚀 So nutzt du dieses Repository

### 1. Projekt öffnen

Beispiel:

```bash
beginner/project-01-3-tier-todo.md
```

---

### 2. Zum Starter Workload wechseln

```bash
cd workloads/3-tier-app
```

---

### 3. Selbst lösen

Öffne die Lösungen nicht sofort.

Das Ziel ist es, echtes Verständnis aufzubauen.

---

### 4. Lösungen nur bei Bedarf nutzen

Vergleiche Architekturentscheidungen, aber kopiere nicht blind.

---

# 🧠 Philosophie

Man lernt Kubernetes nur wirklich, wenn:

- Dinge kaputtgehen
- und man sie selbst repariert

Nicht durch das Auswendiglernen von YAML.

Nicht durch das Bestehen von Prüfungen.

Sondern durch wiederholtes Deployen, Debuggen und Wiederaufbauen von Systemen.

---

# 🗺️ Roadmap

Zukünftige Themen:

**Beginner**
- Stateless Deployments
- Background Worker
- Stateful Systeme

**Intermediate**
- Autoscaling
- Observability Stacks
- Messaging Systeme

**Advanced**
- GitOps Pipelines
- Service Mesh
- Multi-Cluster Plattformen
- Production Security Hardening

---

# 🤝 Beiträge

Ideen und Verbesserungen sind willkommen über Issues und Pull Requests.