```markdown
# Kubernetes Objects: Advanced Guide for Administrators (Part - 2)

## Introduction

This tutorial expands upon the foundational concepts of Kubernetes by covering additional objects that are essential for cluster management, security, and workload customization. It includes Pods, ClusterRoles, PersistentVolumes (PV), PersistentVolumeClaims (PVC), StorageClasses, RoleBindings, NetworkPolicies, Static Pods, Jobs, and Custom Schedulers.

---

## 1. Pod
**Description**: A Pod is the smallest deployable unit in Kubernetes, representing a single instance of a running process in a cluster. Pods can contain one or more tightly coupled containers.

**Use Case**:
- Deploy standalone applications or as building blocks for higher-level controllers like Deployments.

**Example**:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
```

**Command**:
```bash
kubectl apply -f nginx-pod.yaml
kubectl get pods
kubectl describe pod nginx-pod
```

---

## 2. PersistentVolume (PV)
**Description**: A PersistentVolume (PV) is a cluster-wide resource that provides durable storage for Pods.

**Use Case**:
- Provide storage that persists beyond the lifecycle of a Pod.

**Example**:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-example
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/pv-example
```

**Command**:
```bash
kubectl apply -f pv-example.yaml
kubectl get pv
```

---

## 3. PersistentVolumeClaim (PVC)
**Description**: A PersistentVolumeClaim (PVC) is a request for storage by a Pod. PVCs are bound to PVs.

**Use Case**:
- Request specific storage capacity and access modes for Pods.

**Example**:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-example
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

**Command**:
```bash
kubectl apply -f pvc-example.yaml
kubectl get pvc
```

---

## 4. StorageClass
**Description**: A StorageClass defines the types of storage available in a cluster and the provisioner used to create PersistentVolumes dynamically.

**Use Case**:
- Automate PV provisioning based on predefined parameters.

**Example**:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
```

**Command**:
```bash
kubectl apply -f storage-class.yaml
kubectl get storageclass
```

---

## 5. ClusterRole
**Description**: A ClusterRole grants permissions across the entire cluster, such as managing nodes or non-namespaced resources.

**Use Case**:
- Grant access to cluster-wide resources like nodes, PersistentVolumes, or APIs.

**Example**:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin-role
rules:
- apiGroups: [""]
  resources: ["pods", "nodes"]
  verbs: ["get", "list", "watch"]
```

**Command**:
```bash
kubectl apply -f cluster-role.yaml
kubectl get clusterrole
```

---

## 6. ClusterRoleBinding
**Description**: A ClusterRoleBinding binds a ClusterRole to a user, group, or service account.

**Use Case**:
- Assign cluster-wide permissions to a specific user or group.

**Example**:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-binding
subjects:
- kind: User
  name: admin-user
roleRef:
  kind: ClusterRole
  name: cluster-admin-role
  apiGroup: rbac.authorization.k8s.io
```

**Command**:
```bash
kubectl apply -f cluster-role-binding.yaml
kubectl get clusterrolebindings
```

---

## 7. Role and RoleBinding
**Description**: A Role provides permissions within a specific namespace, and a RoleBinding assigns the Role to a user, group, or service account.

**Use Case**:
- Enforce namespace-scoped RBAC policies.

**Example Role**:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: namespace-admin
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

**Example RoleBinding**:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: namespace-admin-binding
  namespace: dev
subjects:
- kind: User
  name: dev-user
roleRef:
  kind: Role
  name: namespace-admin
  apiGroup: rbac.authorization.k8s.io
```

---

## 8. NetworkPolicy
**Description**: NetworkPolicies control network traffic to and from Pods based on rules.

**Use Case**:
- Restrict Pod communication for security purposes.

**Example**:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-web
  namespace: dev
spec:
  podSelector:
    matchLabels:
      app: web
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: backend
```

**Command**:
```bash
kubectl apply -f network-policy.yaml
kubectl get networkpolicy
```

---

## 9. Static Pods
**Description**: Static Pods are managed directly by the kubelet and are not part of the Kubernetes API server.

**Use Case**:
- Run essential system-level services or critical applications on specific nodes.

**Example**:
Place the following YAML on the node's static pod directory (e.g., `/etc/kubernetes/manifests/`):
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.21
```

**Command**:
```bash
kubectl get pods --all-namespaces
```

---

## 10. Job
**Description**: A Job runs a single task to completion and ensures successful execution.

**Use Case**:
- Perform data processing tasks or database migrations.

**Example**:
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: process-data
spec:
  template:
    spec:
      containers:
      - name: processor
        image: data-processor:latest
      restartPolicy: OnFailure
```

**Command**:
```bash
kubectl apply -f job.yaml
kubectl get jobs
```

---

## 11. Custom Scheduler
**Description**: A custom scheduler allows for advanced scheduling policies beyond the default Kubernetes scheduler.

**Use Case**:
- Implement custom logic to schedule Pods based on custom metrics or node attributes.

**Example**:
Create a Pod using a custom scheduler:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: custom-scheduler-pod
spec:
  schedulerName: custom-scheduler
  containers:
  - name: nginx
    image: nginx:1.21
```

**Command**:
```bash
kubectl apply -f custom-scheduler-pod.yaml
kubectl get pods
```

---

## Summary Table

| **Object**           | **Description**                                         | **Use Case**                                     | **Example Scenario**                        |
|-----------------------|---------------------------------------------------------|-------------------------------------------------|--------------------------------------------|
| **Pod**               | Smallest deployable unit                                | Deploy simple applications                      | Single web server                           |
| **PersistentVolume**  | Provides cluster-wide durable storage                  | Static storage for Pods                         | Local or cloud-based storage                |
| **PersistentVolumeClaim** | Requests storage from a PV                           | Attach storage to Pods                          | Bind storage dynamically                    |
| **StorageClass**      | Defines dynamic storage provisioning                    | Automate PV creation                            | AWS EBS, GCE PD provisioning                |
| **ClusterRole**       | Grants cluster-wide permissions                        | Manage non-namespaced resources                | Node management                             |
| **ClusterRoleBinding**| Binds a ClusterRole to users, groups, or service accounts | Grant global admin permissions                 | Admin access for a user                     |
| **Role**              | Grants namespace-specific permissions                  | Enforce scoped RBAC policies                   | Read-only access to `dev` namespace         |
| **RoleBinding**       | Binds a Role to users, groups, or service accounts      | Assign namespace-specific permissions          | Developer access to a specific namespace    |
| **NetworkPolicy**     | Controls network traffic                               | Restrict Pod communication                     | Allow traffic only from specific Pods       |
| **Static Pod**        | Managed directly by kubelet                            | Deploy critical services on specific nodes     | Node-level logging agents                   |


| **Job**               | Runs a task to completion                              | Process data or perform batch operations       | ETL jobs                                    |
| **Custom Scheduler**  | Implements custom scheduling logic                     | Advanced Pod scheduling                         | Schedule Pods based on custom metrics       |

---

## Conclusion

This tutorial provides an advanced understanding of Kubernetes objects critical for cluster security, storage, and workload management. By mastering these objects, administrators can ensure robust and scalable Kubernetes operations.
