
# Kubernetes CIS Benchmark Detailed Summary

| Category / Subsection                            | PASS | FAIL | WARN |
|-------------------------------------------------|:----:|:----:|:----:|
| 1. Control Plane Security Configuration         |      |      |      |
| └─ 1.1 Control Plane Node Configuration Files  | 0    | 18   | 3    |
| └─ 1.2 API Server                               | 9    | 7    | 5    |
| └─ 1.3 Controller Manager                        | 5    | 0    | 1    |
| └─ 1.4 Scheduler                                | 1    | 1    | 0    |
| 2. Etcd Node Configuration                       |      |      |      |
| └─ 2.1-2.7 Etcd Node Config                     | 7    | 0    | 0    |
| 3. Control Plane Configuration                   |      |      |      |
| └─ 3.1 Authentication and Authorization        | 0    | 0    | 3    |
| └─ 3.2 Logging                                  | 1    | 0    | 1    |
| 4. Worker Node Security Configuration           |      |      |      |
| └─ 4.1 Worker Node Configuration Files         | 2    | 5    | 4    |
| └─ 4.2 Kubelet                                  | 5    | 3    | 3    |
| └─ 4.3 kube-proxy                               | 1    | 0    | 0    |
| 5. Kubernetes Policies                           |      |      |      |
| └─ 5.1 RBAC and Service Accounts                | 2    | 4    | 6    |
| └─ 5.2 Pod Security Standards                   | 2    | 0    | 9    |
| └─ 5.3 Network Policies and CNI                 | 0    | 0    | 2    |
| └─ 5.4 Secrets Management                       | 0    | 0    | 2    |
| └─ 5.5 Extensible Admission Control             | 0    | 0    | 1    |
| └─ 5.7 General Policies                          | 0    | 0    | 4    |
| **Total**                                       | 43   | 38   | 49   |
