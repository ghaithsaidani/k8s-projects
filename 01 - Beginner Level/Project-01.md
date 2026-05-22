# 🧩 Challenge - Multi-Tier Todo App on Kubernetes

> **Level:** Beginner
> **Prerequisites:** CKA (or equivalent), Docker basics, working `kubectl` against a cluster
> **Estimated time:** 8–12 hours
> **Mode:** 🥷 Self-directed. Try every task on your own first. Only open the solution after you've genuinely attempted it (or are stuck for more than ~20 minutes).

---

## 🎯 The Mission

You are given a working 3-tier Todo app (React + Vite frontend, FastAPI backend, PostgreSQL database). Right now it runs locally with `docker-compose` for the DB and dev servers for the app tiers.

**Your job:** ship it to Kubernetes. Properly.

No hand-holding - figure out the manifests, the workload types, the networking, and the storage yourself. Each task below has a hidden solution you can reveal only **after** you've tried.

---
## 📦 Starting Point

The starter project for this challenge is already provided in the repository:

```bash
../workloads/3-tier-app
```

Before doing anything else, move into it:

```bash
cd ../workloads/3-tier-app
```

This is the intentionally incomplete version of the application.

It currently:

- Runs locally with `docker-compose` for PostgreSQL
- Runs the frontend with the Vite dev server
- Runs the backend with FastAPI locally
- Is **not containerized for Kubernetes**
- Has **no Kubernetes manifests**

Your mission is to take this raw application and make it production-ready on Kubernetes from scratch.

Project structure:

```txt
.
├── docker-compose.yml
├── backend/
└── frontend/
```

---

## 🏁 Final Goal

When you're done:

- [ ] `http://todo.local` loads the Todo UI in a browser
- [ ] You can create, list, update, and delete todos
- [ ] Deleting the database pod does **not** lose data
- [ ] DB credentials live in a `Secret`, non-secret config in a `ConfigMap`
- [ ] No `NodePort` or `LoadBalancer` Services - external traffic flows through an `Ingress` only
- [ ] `kubectl apply -f k8s/` deploys the whole stack on a fresh cluster

---

## 🧗 Challenges

### Challenge 1 - Pick & Bootstrap a Cluster

Get a working local Kubernetes cluster where you can later expose an Ingress on `http://todo.local`.

<details>
<summary>💡 Hints</summary>

- `kind`, `minikube`, and `k3d` all work. `kind` is the lightest.
- You'll need port 80 reachable from your host - think about extra port mappings.
- `kubectl get nodes` should return `Ready`.

</details>

<details>
<summary>✅ Solution</summary>

Use `kind` with a cluster config that maps host ports 80 and 443 to the control-plane node:

```yaml
# kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 80
        hostPort: 80
      - containerPort: 443
        hostPort: 443
```

```bash
kind create cluster --config kind-config.yaml
kubectl get nodes
```

</details>

---

### Challenge 2 - Containerize Both App Tiers

Neither tier is dockerized. Write a `Dockerfile` for each. The frontend should be a **production build** served by a static web server, not the Vite dev server.

<details>
<summary>💡 Hints</summary>

- Backend: `python:3.11-slim` is enough. Bind to `0.0.0.0`, not `127.0.0.1`.
- Frontend: multi-stage build - Node to build, nginx to serve.
- After building, you must load the images into your cluster (`kind load docker-image ...`) unless you push to a registry.

</details>

<details>
<summary>✅ Solution</summary>

**`backend/Dockerfile`**

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**`frontend/Dockerfile`**

```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

**Build & load:**

```bash
docker build -t todo-backend:v1 ./backend
docker build -t todo-frontend:v1 ./frontend
kind load docker-image todo-backend:v1
kind load docker-image todo-frontend:v1
```

</details>

---

### Challenge 3 - Namespace & Project Layout

Don't pollute `default`. Pick a clean structure for your manifests.

<details>
<summary>💡 Hints</summary>

- One namespace for the whole app.
- One file per resource (or per tier) inside a `k8s/` folder.
- Make the namespace the default for your current `kubectl` context.

</details>

<details>
<summary>✅ Solution</summary>

```bash
kubectl create namespace todo-app
kubectl config set-context --current --namespace=todo-app
```

```
k8s/
├── namespace.yaml
├── configmap.yaml
├── secret.yaml
├── postgres-statefulset.yaml
├── postgres-service.yaml
├── backend-deployment.yaml
├── backend-service.yaml
├── frontend-deployment.yaml
├── frontend-service.yaml
└── ingress.yaml
```

</details>

---

### Challenge 4 - Separate Config from Secrets

The backend needs: DB host, DB port, DB name, DB user, DB password. Decide what belongs in a `ConfigMap` vs a `Secret`, and write both.

<details>
<summary>💡 Hints</summary>

- Anything that's a credential → `Secret`.
- Anything safe to log/share → `ConfigMap`.
- `stringData:` is more readable than `data:` (no base64).
- Refactor the backend to read these as env vars instead of using a hardcoded `.env`.

</details>

<details>
<summary>✅ Solution</summary>

**`k8s/configmap.yaml`**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: backend-config
data:
  POSTGRES_HOST: postgres
  POSTGRES_PORT: "5432"
  POSTGRES_DB: todos
```

**`k8s/secret.yaml`**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: postgres-secret
type: Opaque
stringData:
  POSTGRES_USER: postgres
  POSTGRES_PASSWORD: ChangeMe123!
```

Backend code should build `DATABASE_URL` from these env vars at startup.

</details>

---

### Challenge 5 - Deploy PostgreSQL with Persistent Storage

Deploy Postgres so that **deleting the pod does not lose data**. Decide which workload type fits.

<details>
<summary>💡 Hints</summary>

- A `Deployment + PVC` works, but a `StatefulSet` is the idiomatic choice for stateful workloads. Use the latter.
- Pair it with a **headless** `Service` (`clusterIP: None`).
- `volumeClaimTemplates` gives each replica its own PVC automatically.
- Inject creds from the Secret you wrote in Challenge 4.

</details>

<details>
<summary>✅ Solution</summary>

**`k8s/postgres-statefulset.yaml`**

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: postgres
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: postgres:16-alpine
          ports:
            - containerPort: 5432
          envFrom:
            - secretRef:
                name: postgres-secret
          env:
            - name: POSTGRES_DB
              valueFrom:
                configMapKeyRef:
                  name: backend-config
                  key: POSTGRES_DB
            - name: PGDATA
              value: /var/lib/postgresql/data/pgdata
          volumeMounts:
            - name: data
              mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 5Gi
```

**`k8s/postgres-service.yaml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres
spec:
  clusterIP: None
  selector:
    app: postgres
  ports:
    - port: 5432
      targetPort: 5432
```

**Verify persistence:**

```bash
kubectl exec -it postgres-0 -- psql -U postgres -d todos -c "CREATE TABLE ping(id int);"
kubectl delete pod postgres-0
# wait for it to come back
kubectl exec -it postgres-0 -- psql -U postgres -d todos -c "\dt"  # ping should still exist
```

</details>

---

### Challenge 6 - Deploy the Backend

Run 2 replicas of the FastAPI backend. It must read its config from the `ConfigMap` and `Secret`, have proper health probes, and resource limits.

<details>
<summary>💡 Hints</summary>

- `envFrom` lets you load an entire ConfigMap/Secret at once.
- `readinessProbe` and `livenessProbe` should hit a real endpoint (`/docs` works if there's no `/health`).
- Always set `requests` and `limits` - even small ones.
- Service should be `ClusterIP` (internal only).

</details>

<details>
<summary>✅ Solution</summary>

**`k8s/backend-deployment.yaml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: todo-backend:v1
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8000
          envFrom:
            - configMapRef:
                name: backend-config
            - secretRef:
                name: postgres-secret
          readinessProbe:
            httpGet:
              path: /docs
              port: 8000
            initialDelaySeconds: 5
            periodSeconds: 5
          livenessProbe:
            httpGet:
              path: /docs
              port: 8000
            initialDelaySeconds: 15
            periodSeconds: 10
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
            limits:
              cpu: 500m
              memory: 256Mi
```

**`k8s/backend-service.yaml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  selector:
    app: backend
  ports:
    - port: 8000
      targetPort: 8000
```

</details>

---

### Challenge 7 - Deploy the Frontend

Run 2 replicas of the nginx-served frontend. The frontend must talk to the backend **through the Ingress** (e.g., `/api/...`), not directly to `localhost:8000`.

<details>
<summary>💡 Hints</summary>

- You'll need to change the API base URL in the React app to a relative path like `/api`.
- The frontend's nginx config can also proxy `/api` to the backend Service - but it's cleaner to let the Ingress handle routing.
- Service stays `ClusterIP`.

</details>

<details>
<summary>✅ Solution</summary>

**`frontend/nginx.conf`**

```nginx
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;

  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

**`k8s/frontend-deployment.yaml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: todo-frontend:v1
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 50m
              memory: 64Mi
            limits:
              cpu: 200m
              memory: 128Mi
```

**`k8s/frontend-service.yaml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  selector:
    app: frontend
  ports:
    - port: 80
      targetPort: 80
```

In `App.jsx`, change `axios.get("http://localhost:8000/todos")` → `axios.get("/api/todos")`.

</details>

---

### Challenge 8 - Expose Everything via an Ingress

Install an Ingress controller and route:

- `http://todo.local/api/*` → backend
- `http://todo.local/*` → frontend

<details>
<summary>💡 Hints</summary>

- NGINX Ingress is the default choice. There's a kind-specific manifest you can apply.
- Use a `rewrite-target` annotation so `/api/todos` reaches the backend as `/todos`.
- Add `127.0.0.1 todo.local` to your `/etc/hosts`.

</details>

<details>
<summary>✅ Solution</summary>

**Install NGINX Ingress on kind:**

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=180s
```

**`k8s/ingress.yaml`**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: todo-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
    - host: todo.local
      http:
        paths:
          - path: /api(/|$)(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: backend
                port:
                  number: 8000
          - path: /()(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: frontend
                port:
                  number: 80
```

```bash
echo "127.0.0.1 todo.local" | sudo tee -a /etc/hosts
# Open http://todo.local
```

</details>

---

### Challenge 9 - Break It on Purpose

You're not done until you've tried to break your own deployment and understood what happens.

<details>
<summary>💡 What to try</summary>

- Delete the backend pods → does the Deployment recreate them?
- Scale backend to 0 → what does the UI show?
- Delete `postgres-0` → does the data survive?
- Change a value in the Secret → do the backend pods pick it up automatically? (Spoiler: no, you have to restart them.)
- `kubectl describe` a pod that's failing - read the events.

</details>

<details>
<summary>✅ What you should observe</summary>

- Deployments self-heal; StatefulSet pods come back with the same name and re-mount the same PVC.
- Updating a Secret does **not** restart pods. You need `kubectl rollout restart deploy/backend`.
- A pod stuck in `Pending` is usually a scheduling or PVC issue - `kubectl describe pod` always tells you why.
- A pod in `CrashLoopBackOff` → check `kubectl logs` and the previous container's logs with `--previous`.

</details>

---

## 🏆 Stretch Goals

Once the base challenge works, level up:

1. Add a `HorizontalPodAutoscaler` for the backend (needs `metrics-server`).
2. Add `ResourceQuota` and `LimitRange` to the namespace.
3. Write `NetworkPolicy` resources: default-deny + explicit allows for frontend→backend and backend→postgres.
4. Add an `initContainer` to the backend that waits for Postgres to be reachable before starting.
5. Package everything as a **Helm chart**.
6. Convert it to **Kustomize** with `base/` and `overlays/dev|prod/`.
7. Install `cert-manager` and serve `https://todo.local` with a self-signed issuer.

---

## 🆘 Stuck?

Reach for these before opening the solutions:

```bash
kubectl get all -n todo-app
kubectl describe pod <name>
kubectl logs -f deploy/backend
kubectl logs <pod> --previous          # for crashed pods
kubectl exec -it deploy/backend -- sh
kubectl run -it --rm netshoot --image=nicolaka/netshoot -- bash   # DNS / network debug
```

If you're stuck for **20+ minutes** on the same error, open the solution. Don't burn out - the goal is to learn, not to suffer.

Good luck. 🚀