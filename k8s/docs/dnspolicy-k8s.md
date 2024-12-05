# **Lab: Configuring DNS for Kubernetes Services and Pods**

This lab demonstrates how to configure and verify DNS in Kubernetes, ensuring proper network resolution and connectivity for services and pods.

---

## **Objective**
1. Understand and verify the default DNS setup in Kubernetes.
2. Perform DNS queries to test connectivity.
3. Configure DNS policies and custom DNS configurations for pods.

---

## **Prerequisites**
- A Kubernetes cluster with tools installed: `kubectl`, `kubeadm`, `kubelet`.
- Basic knowledge of YAML files and Kubernetes commands.

---

## **1. Verifying the Default DNS Setup**

### **Step 1: Check CoreDNS Deployment**
To identify the CoreDNS deployment running in the cluster:
```bash
kubectl get deploy coredns -n kube-system
```

### **Step 2: Check CoreDNS Pods**
To list the CoreDNS pods:
```bash
kubectl get pods -l k8s-app=kube-dns -n kube-system
```

### **Step 3: Check CoreDNS Service**
To view the CoreDNS service:
```bash
kubectl get svc kube-dns -n kube-system
```

### **Step 4: Check DNS Endpoints**
To verify CoreDNS endpoints:
```bash
kubectl get endpoints kube-dns -n kube-system
kubectl describe endpoints kube-dns -n kube-system
```

---

## **2. Executing DNS Queries**

### **Step 1: Create an NGINX Deployment**
Create a YAML file for an NGINX deployment:

**`nginx.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
```

Apply the file:
```bash
kubectl apply -f nginx.yaml
```

### **Step 2: Create a Service for NGINX**
Create a service to expose the NGINX deployment:

**`my-nginx-service.yaml`**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
  selector:
    run: my-nginx
```

Apply the file:
```bash
kubectl apply -f my-nginx-service.yaml
```

Verify the service:
```bash
kubectl get svc my-nginx
kubectl get ep my-nginx
```

### **Step 3: Test DNS Resolution**
Run a pod with network tools to test DNS queries:
```bash
kubectl run curl --image=radial/busyboxplus:curl -i --tty
```

Inside the pod:
```bash
nslookup google.com
nslookup my-nginx
nslookup my-nginx.default.svc.cluster.local
curl my-nginx
```

Exit the pod:
```bash
exit
```

---

## **3. Configuring DNS Policies**

### **Step 1: Create a DNS Policy**
Create a YAML file for a pod with a custom DNS policy:

**`dnspolicy.yaml`**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
  - image: busybox:1.28
    command:
      - sleep
      - "3600"
    name: busybox
  restartPolicy: Always
  hostNetwork: true
  dnsPolicy: ClusterFirstWithHostNet
```

Apply the file:
```bash
kubectl apply -f dnspolicy.yaml
```

Verify the pod:
```bash
kubectl get pods
kubectl describe pod busybox
```

---

## **4. Creating Custom DNS Configurations**

### **Step 1: Define a Custom DNS Configuration**
Create a YAML file for a pod with custom DNS settings:

**`dnsconfig.yaml`**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dnscustomconfig
  namespace: default
spec:
  containers:
    - name: test
      image: nginx
  dnsPolicy: "None"
  dnsConfig:
    nameservers:
      - 1.2.3.4
    searches:
      - ns1.svc.cluster-domain.example
      - my.dns.search.suffix
    options:
      - name: ndots
        value: "2"
      - name: edns0
```

Apply the file:
```bash
kubectl apply -f dnsconfig.yaml
```

Verify the DNS configuration inside the pod:
```bash
kubectl exec -it dnscustomconfig -- cat /etc/resolv.conf
```

---

## **What We’re Trying to Accomplish**

1. **Default DNS Validation**: Ensure the CoreDNS setup in the cluster is functional for service discovery.
2. **DNS Queries**: Verify service name resolution (e.g., `my-nginx`) to test internal and external DNS resolution.
3. **Custom DNS Configuration**: Understand how to customize DNS policies for specific pods.

---

## **Verification**

1. Validate DNS resolution by performing `nslookup` and `curl` commands for:
   - External domains (e.g., `google.com`).
   - Kubernetes service domains (e.g., `my-nginx.default.svc.cluster.local`).
2. Confirm custom DNS settings inside the pod by checking `/etc/resolv.conf`.

---

## **Clean Up**

To delete all resources created during this lab:
```bash
kubectl delete -f nginx.yaml
kubectl delete -f my-nginx-service.yaml
kubectl delete -f dnspolicy.yaml
kubectl delete -f dnsconfig.yaml
```

---

This lab demonstrates how to configure and test DNS for services and pods in Kubernetes. By following these steps, you ensure proper name resolution and connectivity in your Kubernetes cluster. Let me know if you encounter any issues!
