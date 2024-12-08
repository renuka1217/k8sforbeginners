# Lab Tutorial: Taints and Tolerations in Kubernetes

## Table of Contents
1. [**Overview of Taints and Tolerations**](#overview-of-taints-and-tolerations)
2. [**Setting Up the Lab Environment**](#setting-up-the-lab-environment)
3. [**Applying Taints to All Worker Nodes**](#applying-taints-to-all-worker-nodes)
4. [**Deploying a Pod Without Tolerations**](#deploying-a-pod-without-tolerations)
5. [**Adding Tolerations to the Pod**](#adding-tolerations-to-the-pod)
6. [**Validating Pod Scheduling**](#validating-pod-scheduling)
7. [**Summary and Key Takeaways**](#summary-and-key-takeaways)

---

## 1. Overview of Taints and Tolerations

Taints and tolerations allow control over pod scheduling:
- **Taints** repel pods from nodes.
- **Tolerations** allow pods to override taints and schedule on tainted nodes.

Use Cases:
- Dedicated nodes for specific workloads.
- Prevent pods from being scheduled during maintenance or upgrades.
- Enforce node isolation for critical applications.

---

## 2. Setting Up the Lab Environment

Confirm your Kubernetes cluster setup with two worker nodes (`worker-node-1`, `worker-node-2`) and one master node (`master`). Verify the node names:

```bash
kubectl get nodes
```

Output:
```plaintext
NAME              STATUS   ROLES           AGE   VERSION
master            Ready    control-plane   10d   v1.26
worker-node-1     Ready    <none>          10d   v1.26
worker-node-2     Ready    <none>          10d   v1.26
```

---

## 3. Applying Taints to All Worker Nodes

Taint both `worker-node-1` and `worker-node-2` to ensure pods without tolerations cannot be scheduled on either node.

### **Commands to Apply Taints**
For `worker-node-1`:
```bash
kubectl taint nodes worker-node-1 key=example-taint:NoSchedule
```

For `worker-node-2`:
```bash
kubectl taint nodes worker-node-2 key=example-taint:NoSchedule
```

### **Verify Taints**
Check that taints are applied to both nodes:

```bash
kubectl describe node worker-node-1 | grep -i taints
kubectl describe node worker-node-2 | grep -i taints
```

Expected output:
```plaintext
Taints: key=example-taint:NoSchedule
```

---

## 4. Deploying a Pod Without Tolerations

### **Pod YAML Without Tolerations**

Create the following YAML file (`no-tolerations-pod.yaml`):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: no-tolerations-pod
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

Apply the configuration:

```bash
kubectl apply -f no-tolerations-pod.yaml
```

### **Expected Behavior**

Check the pod's status:

```bash
kubectl get pods -o wide
```

The pod will remain in a `Pending` state because both worker nodes are tainted, and the pod cannot tolerate the taints:

```plaintext
NAME                  READY   STATUS    NODE
no-tolerations-pod    0/1     Pending   <none>
```

### **Describe the Pod**

To confirm the scheduling issue, describe the pod:

```bash
kubectl describe pod no-tolerations-pod
```

Look for the `Events` section, which will show a message like:
```plaintext
0/2 nodes are available: 2 node(s) had taint {key: example-taint}, that the pod didn't tolerate.
```

---

## 5. Adding Tolerations to the Pod

### **Pod YAML With Tolerations**

Create a new YAML file (`tolerations-pod.yaml`):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: tolerations-pod
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "example-taint"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```

Explanation:
- **`key`, `value`**: Match the taint key-value pair on the nodes.
- **`effect`**: `NoSchedule` ensures the toleration aligns with the node taint.

Apply the updated pod configuration:

```bash
kubectl apply -f tolerations-pod.yaml
```

---

## 6. Validating Pod Scheduling

### **Check Pod Status**

Verify that the pod is now running:

```bash
kubectl get pods -o wide
```

Expected output:
```plaintext
NAME               READY   STATUS    NODE
tolerations-pod    1/1     Running   worker-node-1
```

If `worker-node-1` is busy, the pod will be scheduled on `worker-node-2`.

### **Describe the Pod**

To ensure the tolerations are applied correctly, describe the pod:

```bash
kubectl describe pod tolerations-pod
```

Check the `Tolerations` section in the output to confirm the pod tolerates the taint.

---

## 7. Summary and Key Takeaways

### **What We Learned**
1. Taints repel pods from nodes unless explicitly tolerated.
2. Tolerations allow pods to override taints and schedule on tainted nodes.

### **Use Cases**
- Reserve nodes for critical workloads or GPU-based tasks.
- Prevent general workloads during node maintenance or upgrade operations.

### **Verification Steps**
1. Apply taints to all worker nodes to repel pods without tolerations.
2. Confirm that pods without tolerations remain in the `Pending` state.
3. Add tolerations to the pod and validate that it gets scheduled on a tainted node.

This tutorial ensures a clear understanding of how taints and tolerations work in real-world scenarios, with verification steps to validate pod scheduling behavior.
