---
title: Validate Persistent Storage and Benchmark Longhorn
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Validate Persistent Storage and Benchmark Longhorn

### Overview

In this section, you'll create Kubernetes Persistent Volumes using Longhorn, deploy workloads, validate persistent storage functionality, and benchmark storage performance using fio.

You will learn how to:

- Create Persistent Volume Claims (PVC)
- Deploy applications using Longhorn storage
- Validate persistent storage functionality
- Run fio storage benchmarks on Kubernetes volumes


### Create Persistent Volume Claim

Create PVC configuration:

```bash
cat > pvc.yaml <<EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: longhorn-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: longhorn
  resources:
    requests:
      storage: 5Gi
EOF
```

Apply PVC:

```bash
kubectl apply -f pvc.yaml
```

### Verify Persistent Volume Claim

```bash
kubectl get pvc
```

The output is similar to:

```output
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
longhorn-pvc   Bound    pvc-14ab1c22-be1c-4706-b9bc-f5b228007814   5Gi        RWO            longhorn       <unset>                 7s
```

### Deploy Test Application

Create NGINX pod using Longhorn storage:

```bash
cat > nginx-longhorn.yaml <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: nginx-longhorn
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - mountPath: "/usr/share/nginx/html"
      name: longhorn-storage
  volumes:
  - name: longhorn-storage
    persistentVolumeClaim:
      claimName: longhorn-pvc
EOF
```

Deploy pod:

```bash
kubectl apply -f nginx-longhorn.yaml
```

### Verify Application Pod

```bash
kubectl get pods
```

The output is similar to:

```output
NAME             READY   STATUS    RESTARTS   AGE
nginx-longhorn   1/1     Running   0          31s
```

### Write Data to Persistent Volume

Open shell inside container:

```bash
kubectl exec -it nginx-longhorn -- bash
```

Write data:

```bash
echo "Longhorn Storage Working on ARM64" > /usr/share/nginx/html/index.html
```

Exit container:

```bash
exit
```

### Validate Persistent Storage

Delete pod:

```bash
kubectl delete pod nginx-longhorn
```

Recreate pod:

```bash
kubectl apply -f nginx-longhorn.yaml
```

Verify data persists:

```bash
kubectl exec -it nginx-longhorn -- cat /usr/share/nginx/html/index.html
```

The output is similar to:

```output
Longhorn Storage Working on ARM64
```

### Create fio Benchmark Pod

Create benchmark pod:

```bash
cat > fio-pod.yaml <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: fio-test
spec:
  containers:
  - name: fio
    image: ubuntu
    command: ["/bin/bash", "-c"]
    args:
      - apt update && apt install -y fio && sleep infinity
    volumeMounts:
    - mountPath: /data
      name: longhorn-storage
  volumes:
  - name: longhorn-storage
    persistentVolumeClaim:
      claimName: longhorn-pvc
EOF
```

Deploy pod:

```bash
kubectl apply -f fio-pod.yaml
```

Wait until pod becomes ready:

```bash
kubectl wait --for=condition=Ready pod/fio-test --timeout=300s
```

### Open fio Container Shell

```bash
kubectl exec -it fio-test -- bash
```

Verify fio installation:

```bash
which fio
```

If fio is not installed:

```bash
apt update
apt install -y fio
```

### Run fio Storage Benchmark

```bash
fio --name=benchmark \
--directory=/data \
--rw=randwrite \
--bs=4k \
--size=1G \
--numjobs=2 \
--time_based \
--runtime=60 \
--group_reporting
```

The output is similar to:

```output
benchmark: Laying out IO file (1 file / 1024MiB)
benchmark: Laying out IO file (1 file / 1024MiB)
Jobs: 2 (f=2): [w(2)][100.0%][eta 00m:00s]
benchmark: (groupid=0, jobs=2): err= 0: pid=3344: Tue May 26 04:06:33 2026
  write: IOPS=40.5k, BW=158MiB/s (166MB/s)(10.0GiB/64649msec); 0 zone resets
    clat (nsec): min=1016, max=46737k, avg=1795.55, stdev=37158.88
     lat (nsec): min=1064, max=46737k, avg=1849.29, stdev=37176.32
    clat percentiles (nsec):
     |  1.00th=[  1176],  5.00th=[  1224], 10.00th=[  1256], 20.00th=[  1304],
     | 30.00th=[  1352], 40.00th=[  1384], 50.00th=[  1432], 60.00th=[  1480],
     | 70.00th=[  1528], 80.00th=[  1608], 90.00th=[  1816], 95.00th=[  2480],
     | 99.00th=[  4048], 99.50th=[  5856], 99.90th=[ 42752], 99.95th=[ 79360],
     | 99.99th=[240640]
   bw (  MiB/s): min=  416, max= 3679, per=100.00%, avg=2048.00, stdev=624.68, samples=20
   iops        : min=106598, max=941978, avg=524288.00, stdev=159917.20, samples=20
  lat (usec)   : 2=92.61%, 4=6.34%, 10=0.67%, 20=0.20%, 50=0.09%
  lat (usec)   : 100=0.06%, 250=0.03%, 500=0.01%, 750=0.01%, 1000=0.01%
  lat (msec)   : 2=0.01%, 4=0.01%, 10=0.01%, 20=0.01%, 50=0.01%
  cpu          : usr=0.81%, sys=6.15%, ctx=13442, majf=0, minf=24
  IO depths    : 1=100.0%, 2=0.0%, 4=0.0%, 8=0.0%, 16=0.0%, 32=0.0%, >=64=0.0%
     submit    : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     complete  : 0=0.0%, 4=100.0%, 8=0.0%, 16=0.0%, 32=0.0%, 64=0.0%, >=64=0.0%
     issued rwts: total=0,2621442,0,0 short=0,0,0,0 dropped=0,0,0,0
     latency   : target=0, window=0, percentile=100.00%, depth=1

Run status group 0 (all jobs):
  WRITE: bw=158MiB/s (166MB/s), 158MiB/s-158MiB/s (166MB/s-166MB/s), io=10.0GiB (10.7GB), run=64649-64649msec

Disk stats (read/write):
  sdb: ios=0/7380, sectors=0/20959744, merge=0/7438, ticks=0/707928, in_queue=707929, util=98.39%
```


You should observe:

- PVC successfully provisioned
- Longhorn storage mounted correctly
- Data persists after pod recreation
- fio benchmark completes successfully
- Stable storage performance on ARM64 Kubernetes

### What You've Learned

In this section, you learned how to:

- Create Persistent Volume Claims using Longhorn
- Deploy applications with persistent storage
- Validate Kubernetes storage persistence
- Run fio benchmarks on Longhorn volumes
- Test Kubernetes storage performance on ARM64
