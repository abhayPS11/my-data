---

title: PostgreSQL Deployment Using Custom Helm Chart (GKE Axion ARM64 Compatible)
weight: 7

### FIXED, DO NOT MODIFY

## layout: learningpathall

## PostgreSQL Deployment Using Custom Helm Chart

This guide explains how to deploy **PostgreSQL** on Kubernetes using a **custom Helm chart** with **persistent storage**, fully compatible with **GKE Axion (C4A Arm64)** nodes.

---

## Goal

After completing this guide, your environment will include:

* PostgreSQL running inside Kubernetes
* Persistent storage using PVC (Axion-compatible)
* Secure credentials using Kubernetes Secrets
* Ability to connect using `psql`
* A clean, reusable Helm chart that works on ARM64

---

## Prerequisites

Ensure Kubernetes and Helm are working:

```console
kubectl get nodes
helm version
```

If these commands fail, fix them before continuing.

---

## ⚠️ Important: GKE Axion (C4A) Requirements

Axion node pools introduce two constraints you **must** handle:

1. **ARM64 taint**

   * Nodes are tainted with: `kubernetes.io/arch=arm64:NoSchedule`
   * Pods must include a toleration.

2. **StorageClass compatibility**

   * Default `standard-rwo` uses `pd-balanced` ❌ (unsupported on C4A)
   * Use `standard` (pd-standard) or a `pd-ssd` based StorageClass.

This guide includes both fixes.

---

## Create Working Directory

Creates a dedicated folder to store Helm charts.

```console
mkdir helm-microservices
cd helm-microservices
```

---

## Create Helm Chart

Generate the Helm chart skeleton:

```console
helm create my-postgres
```

**Directory structure:**

```text
helm-microservices/
└── my-postgres/
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
```

---

## Clean the Chart

Remove unnecessary default templates. Inside `my-postgres/templates/`, delete:

* hpa.yaml
* ingress.yaml
* serviceaccount.yaml
* tests/
* NOTES.txt
* httproute.yaml

Only PostgreSQL-specific templates will be maintained.

---

## Configure `values.yaml` (Main Configuration)

Centralizes all configuration including image, credentials, storage, and ARM tolerations.

Replace **entire contents** of `my-postgres/values.yaml` with:

```yaml
replicaCount: 1

image:
  repository: postgres
  tag: "15"
  pullPolicy: IfNotPresent

postgresql:
  username: admin
  password: admin123
  database: mydb

persistence:
  enabled: true
  size: 10Gi

  # REQUIRED for GKE Axion (C4A)
  # Use pd-standard (supported)
  storageClass: standard

  mountPath: /var/lib/postgresql
  dataSubPath: data

architecture:
  tolerations:
    enabled: true
```

**Why this matters**

* Prevents scheduling failures on ARM64
* Avoids unsupported disk types on C4A
* Simplifies upgrades and maintenance

---

## Create `_helpers.tpl`

Create `my-postgres/templates/_helpers.tpl`:

```yaml
{{- define "my-postgres.name" -}}
my-postgres
{{- end }}

{{- define "my-postgres.fullname" -}}
{{ .Release.Name }}-{{ include "my-postgres.name" . }}
{{- end }}
```

---

## Create `secret.yaml` (Database Credentials)

Create `my-postgres/templates/secret.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: {{ include "my-postgres.fullname" . }}
type: Opaque
stringData:
  POSTGRES_USER: {{ .Values.postgresql.username | quote }}
  POSTGRES_PASSWORD: {{ .Values.postgresql.password | quote }}
  POSTGRES_DB: {{ .Values.postgresql.database | quote }}
```

**Why this matters**

* Avoids hard-coded credentials
* Follows Kubernetes security best practices

---

## Create `pvc.yaml` (Persistent Storage)

Create `my-postgres/templates/pvc.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: {{ include "my-postgres.fullname" . }}-pvc
spec:
  storageClassName: {{ .Values.persistence.storageClass }}
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: {{ .Values.persistence.size }}
```

**Why this matters**

* Ensures Axion-compatible disks
* Prevents volume attach failures

---

## Create `deployment.yaml` (PostgreSQL Pod)

Replace `my-postgres/templates/deployment.yaml` with:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "my-postgres.fullname" . }}

spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "my-postgres.name" . }}

  template:
    metadata:
      labels:
        app: {{ include "my-postgres.name" . }}

    spec:
      {{- if .Values.architecture.tolerations.enabled }}
      tolerations:
        - key: "kubernetes.io/arch"
          operator: "Equal"
          value: "arm64"
          effect: "NoSchedule"
      {{- end }}

      containers:
        - name: postgres
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}

          ports:
            - containerPort: 5432

          envFrom:
            - secretRef:
                name: {{ include "my-postgres.fullname" . }}

          env:
            - name: PGDATA
              value: "{{ .Values.persistence.mountPath }}/{{ .Values.persistence.dataSubPath }}"

          volumeMounts:
            - name: postgres-data
              mountPath: {{ .Values.persistence.mountPath }}

      volumes:
        - name: postgres-data
          persistentVolumeClaim:
            claimName: {{ include "my-postgres.fullname" . }}-pvc
```

**Notes**

* ARM64 toleration allows scheduling on Axion nodes
* `PGDATA` avoids the `lost+found` issue
* PVC is safely mounted

---

## Create `service.yaml` (Internal Access)

Replace `my-postgres/templates/service.yaml` with:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "my-postgres.fullname" . }}
spec:
  type: ClusterIP
  ports:
    - port: 5432
      targetPort: 5432
  selector:
    app: {{ include "my-postgres.name" . }}
```

**ClusterIP**

* PostgreSQL remains accessible only inside the cluster

---

## Install PostgreSQL Using Helm

```console
cd helm-microservices
helm uninstall postgres-app || true
helm install postgres-app ./my-postgres
```

---

## Verify Deployment

```console
kubectl get pods
kubectl get pvc
```

Expected output:

```output
NAME                                        READY   STATUS    AGE
postgres-app-my-postgres-xxxx               1/1     Running   1m

NAME                           STATUS   STORAGECLASS   CAPACITY
postgres-app-my-postgres-pvc   Bound    standard       10Gi
```

---

## Test PostgreSQL

Connect to the database:

```console
kubectl exec -it <postgres-pod> -- psql -U admin -d mydb
```

Expected:

```output
psql (15.x)
mydb=#
```

Run test queries:

```psql
CREATE TABLE test (id INT);
INSERT INTO test VALUES (1);
SELECT * FROM test;
```

Expected:

```output
 id
----
  1
```

---

## Outcome

You have successfully:

* Created an **Axion-safe** custom Helm chart
* Deployed PostgreSQL on **ARM64 GKE**
* Enabled persistent storage with a supported disk type
* Used Secrets for credentials
* Verified database functionality
