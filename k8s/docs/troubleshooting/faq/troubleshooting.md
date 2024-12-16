    
---

### **Logs and Events (100–109)**  
100. **Logs for an application pod are incomplete or truncated. How would you address this issue?**  
   - Ensure proper logging configuration in the application.  
   - Check the log rotation and retention policies on the node.  
   - Validate disk space availability on the node.  

101. **A pod's logs indicate frequent restarts due to an OOMKilled event. What would you do to debug this?**  
   - Check container resource limits: `kubectl describe pod <pod-name>`.  
   - Increase memory limits if necessary or optimize the application.  
   - Monitor memory usage with `kubectl top pod`.  

102. **Events for a deployment show FailedScheduling. What are the possible causes, and how would you troubleshoot this?**  
   - Check node capacity and resource requests.  
   - Verify taints and tolerations for the deployment.  
   - Inspect `kubectl describe pod` for more event details.  

103. **A node reports disk pressure. What logs would you check to diagnose the cause of this issue?**  
   - Inspect `journalctl -u kubelet`.  
   - Check `/var/log/` for disk usage or inode exhaustion.  
   - Use `df -h` and `du -sh` to identify large directories.  

104. **Kubernetes logs indicate connection refused errors from the API server. How would you debug this?**  
   - Check API server pod logs (`kubectl logs -n kube-system`).  
   - Verify control plane node networking and firewall rules.  
   - Test connectivity using `curl <api-server-url>`.  

105. **A pod is evicted due to high memory usage on the node. How would you troubleshoot and prevent this from happening again?**  
   - Use `kubectl describe pod` to confirm eviction reasons.  
   - Adjust resource requests/limits in the pod spec.  
   - Monitor node memory usage and scale up nodes if needed.  

106. **No logs are available for a pod due to the container failing to start. What diagnostic steps would you follow?**  
   - Check the pod's events with `kubectl describe pod`.  
   - Inspect the container runtime logs (e.g., containerd or Docker).  
   - Test image pull availability and configuration.  

107. **An application reports 503 Service Unavailable errors intermittently. How would you diagnose this using logs and events?**  
   - Inspect backend pod logs for crashes or readiness probe failures.  
   - Check Service and Ingress configurations.  
   - Use `kubectl describe service` to verify endpoints.  

108. **Fluentd is not forwarding logs from a node to the central logging system. How would you debug this issue?**  
   - Verify Fluentd configuration files (e.g., `fluent.conf`).  
   - Check Fluentd pod logs for errors.  
   - Confirm the central logging system's accessibility.  

109. **Logs indicate that a pod's readiness probe is failing. How would you identify the cause?**  
   - Inspect the readiness probe configuration in the deployment spec.  
   - Use `kubectl exec` to manually test the probe's endpoint.  
   - Check application logs for startup or health issues.  

---

### **Pod and Deployment Troubleshooting (110–119)**  
110. **A deployment rollout is stuck. What commands and steps would you use to identify the issue?**  
   - Run `kubectl rollout status deployment <deployment-name>`.  
   - Inspect events with `kubectl describe deployment`.  
   - Check for failing pods or invalid configurations.  

111. **Pods are stuck in Pending state due to insufficient CPU or memory on the nodes. How would you resolve this?**  
   - Use `kubectl describe pod` to confirm resource requests.  
   - Scale the cluster or reduce pod resource requests.  
   - Check node taints or affinity rules.  

112. **A pod is stuck in ContainerCreating state. What are the possible causes, and how would you address them?**  
   - Inspect events with `kubectl describe pod`.  
   - Check volume attachment or CNI plugin issues.  
   - Verify image pull accessibility.  

113. **A deployment rollback fails. What could cause this issue, and how would you resolve it?**  
   - Check for missing ReplicaSets from previous revisions.  
   - Ensure proper resource definitions in the rollback spec.  
   - Use `kubectl rollout undo deployment <deployment-name>` for debugging.  

114. **A StatefulSet fails to scale up. How would you troubleshoot the issue?**  
   - Verify PVC bindings for new replicas.  
   - Check StatefulSet pod events for resource conflicts.  
   - Debug networking issues for headless Service communication.  

115. **A Job is not completing successfully. How would you debug the issue?**  
   - Inspect pod logs and events with `kubectl logs` and `kubectl describe`.  
   - Check for missing permissions or invalid configurations.  
   - Verify retries and backoff limits in the Job spec.  

116. **Pods are experiencing high latency when accessing a Persistent Volume. What steps would you take to debug this issue?**  
   - Check storage class performance and backend IOPS.  
   - Monitor disk usage and access patterns on the node.  
   - Test PVC performance independently.  

117. **An Init Container is failing to start. How would you diagnose and fix this issue?**  
   - Use `kubectl logs <pod-name> -c <init-container-name>` to check logs.  
   - Verify resource limits for the Init Container.  
   - Check for missing dependencies or incorrect commands.  

118. **A pod is unable to pull the required container image. What steps would you follow to debug this?**  
   - Verify image name and tag in the pod spec.  
   - Test pulling the image manually from the container runtime.  
   - Check imagePullSecrets for authentication issues.  

119. **A DaemonSet is not scheduling pods on certain nodes. How would you troubleshoot this?**  
   - Verify node labels and DaemonSet nodeSelector rules.  
   - Check node taints and tolerations.  
   - Inspect events for scheduling errors.  

---

### **Cluster and Node Troubleshooting (120–129)**  
120. **A node is marked NotReady. What steps would you follow to identify and fix the issue?**  
   - Use `kubectl describe node` to check conditions.  
   - Inspect kubelet logs (`journalctl -u kubelet`).  
   - Test network connectivity to the API server.  

121. **All worker nodes are unreachable by the control plane. How would you debug and fix the issue?**  
   - Verify control plane network connectivity.  
   - Check the kube-proxy and CNI plugin configurations.  
   - Ensure worker node certificates are valid.  

122. **The kubelet service is failing to start on a node. What logs and configuration files would you check?**  
   - Inspect logs with `journalctl -u kubelet`.  
   - Check kubelet configuration files (`/var/lib/kubelet/config.yaml`).  
   - Validate node certificate expiration.  

123. **A cluster upgrade fails halfway through. How would you proceed to troubleshoot and resolve the issue?**  
   - Inspect `kubeadm` upgrade logs.  
   - Check API server and etcd health.  
   - Manually upgrade components on affected nodes.  

124. **The etcd cluster is reporting quorum not achieved. How would you diagnose and fix this issue?**  
   - Verify etcd member connectivity using `etcdctl`.  
   - Check etcd logs for leader election issues.  
   - Restore etcd from a backup if necessary.  

125. **A node consistently experiences high CPU utilization. How would you identify and resolve the root cause?**  
   - Use `top` or `htop` to monitor processes.  
   - Check for noisy neighbor workloads.  
   - Scale workloads or adjust resource requests/limits.  

126. **Kubernetes reports NodeUnderDiskPressure for a worker node. How would you troubleshoot and fix this?**  
   - Identify large directories using `du -sh`.  
   - Check log rotation and clean up old logs.  
   - Resize disk or reduce pod resource usage.  

127. **A node is unable to register with the API server. What could be the issue, and how would you address it?**  
   - Verify kubelet configuration and certificates.  
   - Check network connectivity to the API server.  
   - Inspect kubelet logs for registration errors.  

128. **A kubectl drain operation fails on a node. How would you identify the cause?**  
   - Use `kubectl describe node` for detailed events.  
   - Check for DaemonSets or pods without proper tolerations.  
   - Force eviction if safe using `kubectl drain --force`.  

129. **A node is unable to mount a Persistent Volume. What troubleshooting steps would you take?**  
   - Check the PV and PVC status using `kubectl describe`.  
   - Verify storage plugin logs on the node.  
   - Test the underlying storage connectivity.  

### **Networking Troubleshooting (130–139)**  

130. **A NetworkPolicy is incorrectly blocking traffic to an application. How would you identify and fix this?**  
   - Use `kubectl describe networkpolicy` to review the applied policies.  
   - Test connectivity using tools like `curl`, `ping`, or `netcat` from other pods.  
   - Check the pod labels to ensure they match the policy selectors.  
   - Adjust the NetworkPolicy rules to allow required traffic.            

131. **An Ingress is not routing traffic to the backend service. How would you debug this?**  
   - Validate Ingress rules with `kubectl describe ingress`.  
   - Check if the Ingress Controller is running and properly configured.  
   - Verify the Service's endpoints with `kubectl describe service`.  
   - Ensure DNS resolves correctly and troubleshoot with tools like `curl` or `wget`.  

132. **Pods are unable to communicate across namespaces despite proper services being in place. How would you approach this issue?**  
   - Check for restrictive NetworkPolicies in either namespace.  
   - Confirm Service discovery is functioning (e.g., DNS resolution of service names).  
   - Verify that the CNI plugin is correctly handling cross-namespace traffic.  

133. **Traffic is being dropped between pods due to a misconfigured CNI plugin. How would you debug and fix this?**  
   - Inspect CNI plugin logs and configurations (e.g., `flannel`, `calico`, `weave`).  
   - Verify CNI plugin installation and check for errors in the control plane logs.  
   - Reapply or reinitialize the CNI configuration if necessary.  

134. **A pod is unable to connect to an external database. What steps would you take to debug the issue?**  
   - Test connectivity using `curl` or `nc` from within the pod.  
   - Check if the external database allows connections from the cluster IP range.  
   - Inspect NetworkPolicies and firewalls for restrictions.  
   - Verify correct database credentials and connection strings.  

135. **An external application cannot connect to a Kubernetes LoadBalancer service. What troubleshooting steps would you follow?**  
   - Confirm LoadBalancer creation with `kubectl describe service`.  
   - Verify the cloud provider's LoadBalancer status (e.g., AWS, GCP, Azure).  
   - Check firewall rules to allow traffic on the required ports.  
   - Inspect pod readiness and ensure endpoints are available.  

136. **Pods are unable to resolve external DNS names. How would you troubleshoot and resolve this issue?**  
   - Verify the CoreDNS pod status with `kubectl get pods -n kube-system`.  
   - Check CoreDNS logs (`kubectl logs -n kube-system <coredns-pod-name>`).  
   - Test DNS resolution from within the pod using `nslookup` or `dig`.  
   - Ensure the `resolv.conf` file in the pod is correctly configured.  

137. **A pod reports connection timeout errors when connecting to another pod. How would you identify the issue?**  
   - Validate the target pod's readiness and IP using `kubectl describe pod`.  
   - Check Service endpoints and DNS resolution.  
   - Test connectivity with `ping` or `curl` from the source pod.  
   - Inspect NetworkPolicy or firewall rules blocking traffic.  

138. **A hostNetwork: true pod is not reachable on its specified IP. What steps would you take to debug this?**  
   - Verify the pod's host network IP with `kubectl describe pod`.  
   - Check for conflicting ports on the host node.  
   - Ensure firewalls allow traffic to the host network.  

139. **A service's endpoints are empty. What could be causing this issue, and how would you resolve it?**  
   - Use `kubectl describe service` to confirm the selector matches pod labels.  
   - Verify that the backend pods are in `Running` and `Ready` states.  
   - Check for missing readiness probes in the pod spec.  

---

### **Storage Troubleshooting (140–149)**  

140. **A Persistent Volume is stuck in Released state. How would you debug and resolve the issue?**  
   - Check if the PV is still bound to a deleted PVC.  
   - Delete and recreate the PV if no data persistence is required.  
   - Use the `reclaimPolicy` to retain or recycle data.  

141. **A pod using a hostPath volume is unable to access the specified directory. How would you troubleshoot this?**  
   - Verify the directory exists on the host node.  
   - Check file permissions for the directory.  
   - Inspect pod events for mount errors.  

142. **A Persistent Volume Claim is stuck in Pending state. What steps would you take to resolve this?**  
   - Check if a suitable Persistent Volume exists with matching specs.  
   - Verify the PVC and StorageClass configuration.  
   - Ensure the dynamic provisioning controller is working for the StorageClass.  

143. **Data corruption occurs on a volume used by multiple pods. How would you identify and fix this issue?**  
   - Ensure the volume supports multi-node access (e.g., ReadWriteMany).  
   - Verify application-level synchronization or locking mechanisms.  
   - Inspect the backend storage system for issues.  

144. **A pod fails to mount a PVC due to insufficient permissions. How would you debug and resolve the issue?**  
   - Check pod events for detailed error messages.  
   - Inspect the underlying storage permissions.  
   - Verify the Kubernetes service account permissions.  

145. **A StatefulSet is not able to attach its required Persistent Volumes. How would you troubleshoot this?**  
   - Check if the required PVCs are bound and available.  
   - Verify node affinity rules for the StatefulSet.  
   - Inspect pod events for mount or scheduling errors.  

146. **A node consistently fails to bind dynamic Persistent Volumes. What diagnostic steps would you take?**  
   - Check the storage provisioner logs.  
   - Verify the StorageClass configuration.  
   - Ensure the node has sufficient storage capacity.  

147. **Data stored on a volume is not persisting after pod restarts. What steps would you follow to debug this?**  
   - Check if the volume type supports persistence (e.g., hostPath is not persistent).  
   - Verify PVC binding and pod volume mounts.  
   - Inspect backend storage for data retention policies.  

148. **A PVC bound to an NFS-based Persistent Volume is experiencing high latency. How would you troubleshoot this?**  
   - Check NFS server logs and health.  
   - Test NFS network performance using tools like `iperf`.  
   - Monitor node and network resource utilization.  

149. **A volume snapshot is failing to be created. How would you debug this issue?**  
   - Verify VolumeSnapshot and VolumeSnapshotClass configurations.  
   - Check the CSI driver logs for errors.  
   - Ensure the backend storage supports snapshots.  

---

### **Control Plane and Component Issues (150)**  

150. **The kube-apiserver is responding slowly to requests. How would you identify the root cause?**  
   - Monitor API server metrics using `kubectl top` or Prometheus.  
   - Check control plane logs for errors or throttling messages.  
   - Validate etcd health and performance.  
   - Review network latency between nodes and the control plane.  

Here are detailed **answers for questions 151–200**, with diagnostic commands and solutions where applicable. The answers include a mix of practical steps, theoretical understanding, and troubleshooting insights.

---

### **Control Plane and Component Issues (151–159)**

151. **The kube-scheduler is failing to schedule pods. What steps would you follow to debug this?**
   - **Commands to check:**
     - `kubectl describe pod <pod-name>` to view scheduling events and errors.
     - `kubectl get nodes` to ensure sufficient resources are available.
   - **Fix:** Check for taints, tolerations, and resource requests that might prevent scheduling. Review the kube-scheduler logs (`journalctl -u kube-scheduler`).

152. **The kube-controller-manager is not reconciling objects. How would you diagnose the issue?**
   - **Commands to check:**
     - Inspect the logs: `journalctl -u kube-controller-manager`.
     - Check API server health: `kubectl get componentstatuses`.
   - **Fix:** Ensure the controller-manager has connectivity to the API server and sufficient resources.

153. **etcd is consuming excessive memory. How would you troubleshoot and optimize its performance?**
   - **Commands to check:**
     - `etcdctl endpoint status` to check health and resource usage.
     - `etcdctl defrag` to free space if data fragmentation is high.
   - **Fix:** Reduce key churn, increase etcd memory, or scale the etcd cluster.

154. **A control plane node runs out of disk space. How would you identify and resolve the issue?**
   - **Commands to check:**
     - `df -h` to view disk usage.
     - Check etcd data directory: `/var/lib/etcd`.
   - **Fix:** Clean up old logs, defrag etcd, and resize the disk if necessary.

155. **A pod cannot communicate with the kube-apiserver. What steps would you take to debug this?**
   - **Commands to check:**
     - Verify pod network connectivity: `ping <apiserver-ip>`.
     - Inspect kube-proxy logs for potential issues.
   - **Fix:** Check API server IP in kubelet configuration and network routing.

156. **The Kubernetes dashboard is inaccessible. What are the possible causes, and how would you fix them?**
   - **Commands to check:**
     - `kubectl get svc -n kubernetes-dashboard` to verify the service type and endpoint.
     - Check the dashboard pod logs: `kubectl logs <dashboard-pod> -n kubernetes-dashboard`.
   - **Fix:** Correct RBAC roles or expose the service using a NodePort or Ingress.

157. **Cluster autoscaler is not scaling nodes as expected. How would you debug and resolve this issue?**
   - **Commands to check:**
     - Inspect autoscaler logs: `kubectl logs -n kube-system <autoscaler-pod>`.
     - Review pending pods with `kubectl get pods --field-selector=status.phase=Pending`.
   - **Fix:** Ensure appropriate scaling policies and sufficient quotas with the cloud provider.

158. **A control plane component is frequently restarting. How would you troubleshoot this issue?**
   - **Commands to check:**
     - `kubectl get events -n kube-system` for relevant messages.
     - Inspect logs (`journalctl -u <component-name>`).
   - **Fix:** Check for resource exhaustion or misconfiguration in systemd or component flags.

159. **The kube-proxy service is not functioning on a node. How would you debug this?**
   - **Commands to check:**
     - Inspect kube-proxy logs: `journalctl -u kube-proxy`.
     - Test connectivity to the cluster IP using `curl`.
   - **Fix:** Verify the CNI plugin, iptables rules, and kube-proxy configuration.

---

### **Monitoring and Observability (160–169)**

160. **Prometheus is not collecting metrics from Kubernetes nodes. How would you troubleshoot this issue?**
   - **Commands to check:**
     - Verify Prometheus targets: `kubectl exec <prometheus-pod> -- curl <target-url>`.
     - Check service discovery configuration in Prometheus.
   - **Fix:** Ensure node exporters are running and scrape configurations are correct.

161. **Grafana dashboards show gaps in metrics data. What steps would you take to debug this?**
   - **Commands to check:**
     - Inspect Prometheus retention and storage configuration.
     - Review Grafana datasource configuration.
   - **Fix:** Allocate more resources for Prometheus or adjust retention settings.

162. **A pod's resource usage spikes unexpectedly. How would you use monitoring tools to identify the root cause?**
   - **Tools:** Use Prometheus and Grafana to review CPU/memory graphs.
   - Check logs of the application for spikes caused by load or memory leaks.

163. **Logs from a specific namespace are missing in your centralized logging system. How would you debug this?**
   - **Commands to check:**
     - Verify Fluentd configuration for the namespace.
     - Inspect Fluentd or Logstash logs for errors.
   - **Fix:** Update logging filters to include the namespace.

164. **Monitoring tools report high API server request latencies. What could be causing this issue?**
   - **Commands to check:**
     - Review API server metrics and logs.
     - Inspect etcd health and network latency.
   - **Fix:** Scale the API server or reduce request volume.

165. **A custom monitoring exporter is not scraping metrics correctly. How would you debug this issue?**
   - **Commands to check:**
     - Test the exporter endpoint: `curl <exporter-url>/metrics`.
     - Review Prometheus scrape configurations.
   - **Fix:** Correct exporter implementation or adjust scrape intervals.

166. **Logs for a specific pod show high volumes of error messages. How would you analyze and address this?**
   - **Commands to check:**
     - Inspect logs: `kubectl logs <pod-name>`.
     - Use logging queries to filter and identify the root cause.
   - **Fix:** Fix the application logic or adjust retries if errors are transient.

167. **Metrics indicate a memory leak in an application. How would you approach debugging this?**
   - **Steps:** Use profiling tools like `pprof` or `heapster` to identify leaks.
   - Fix code to release unused memory.

168. **A pod is using significantly more CPU than allocated. How would you identify the source of the problem?**
   - **Tools:** Use `kubectl top pod` and application-level profiling.
   - Fix by optimizing application code or increasing resource limits.

169. **Alerts from your monitoring system indicate a node is about to run out of resources. How would you prevent downtime?**
   - **Commands to check:**
     - Monitor resource usage: `kubectl describe node`.
     - Drain non-critical pods: `kubectl drain <node-name>`.
   - **Fix:** Scale nodes or optimize resource requests.

---

### **Advanced Troubleshooting Scenarios (170–179)**

170. **The control plane is completely unresponsive. What steps would you take to recover the cluster?**
   - **Steps:** Restore etcd from backup, restart control plane components, and verify API server health.

171. **A cluster backup has failed to restore correctly. How would you debug and fix the restoration process?**
   - **Commands to check:**
     - Verify backup integrity and compatibility with the restore process.
   - **Fix:** Follow vendor-specific restore steps or retry with a validated backup.

---

Here are the answers for **questions 172–179** with detailed steps and possible commands for debugging:

---

172. **A Job is creating too many pods, causing resource contention in the cluster. How would you address this issue?**  
   - **Commands to check:**
     - Review the Job spec:  
       ```bash
       kubectl describe job <job-name>
       ```
     - List pods created by the Job:  
       ```bash
       kubectl get pods --selector=job-name=<job-name>
       ```
   - **Fix:**  
     - Limit `parallelism` in the Job spec to control the number of concurrent pods:  
       ```yaml
       spec:
         parallelism: 3
       ```
     - Ensure proper backoff policy to reduce rapid retries:  
       ```yaml
       backoffLimit: 4
       ```

---

173. **A cluster experiences intermittent network partitions. How would you debug and resolve this issue?**  
   - **Commands to check:**
     - Check node connectivity:  
       ```bash
       kubectl get nodes -o wide
       ```
     - Inspect CNI logs:  
       ```bash
       journalctl -u kubelet
       ```
     - Validate network routes:  
       ```bash
       ip route
       ```
   - **Fix:**  
     - Investigate underlying network hardware or cloud network configurations.  
     - Restart the affected CNI plugin and nodes.  
     - Use `traceroute` to pinpoint network hops causing the issue.

---

174. **An etcd cluster is split-brained. How would you recover and stabilize it?**  
   - **Commands to check:**
     - Check etcd member status:  
       ```bash
       etcdctl member list
       ```
     - Inspect logs for split-brain conditions:  
       ```bash
       journalctl -u etcd
       ```
   - **Fix:**  
     - Remove faulty members using `etcdctl member remove <member-id>`.  
     - Reconfigure etcd to achieve quorum and avoid split-brain:  
       ```bash
       etcdctl member add <new-member> --peer-urls <peer-url>
       ```

---

175. **Autoscaling is causing application instability due to scaling up too quickly. How would you fix this issue?**  
   - **Commands to check:**
     - Inspect HPA or cluster autoscaler logs:  
       ```bash
       kubectl describe hpa <hpa-name>
       ```
     - Monitor metric values causing rapid scaling:  
       ```bash
       kubectl top pod
       ```
   - **Fix:**  
     - Adjust stabilization settings in HPA to prevent rapid scaling:  
       ```yaml
       spec:
         behavior:
           scaleUp:
             stabilizationWindowSeconds: 60
             policies:
               - type: Percent
                 value: 50
                 periodSeconds: 60
       ```
     - Use buffer resources to accommodate sudden spikes without scaling immediately.

---

176. **A complex multi-tier application deployment fails. How would you systematically debug and resolve the issue?**  
   - **Steps:**
     1. Debug tier-by-tier, starting with the first layer (e.g., database, backend, then frontend).  
     2. Check pod logs:  
        ```bash
        kubectl logs <pod-name>
        ```
     3. Validate service connectivity:  
        ```bash
        kubectl exec <pod-name> -- curl <service-url>
        ```
   - **Fix:**  
     - Fix issues at each tier (e.g., misconfigured services, environment variables).  
     - Use `kubectl port-forward` for testing internal service communication.

---

177. **Your cluster is under a heavy DDoS attack. How would you mitigate and secure the cluster?**  
   - **Steps:**
     - Identify attacking IPs using cloud provider’s monitoring tools or Kubernetes logs.  
     - Use NetworkPolicies to block malicious traffic:  
       ```yaml
       apiVersion: networking.k8s.io/v1
       kind: NetworkPolicy
       metadata:
         name: deny-malicious
       spec:
         podSelector: {}
         ingress:
           - from:
               - ipBlock:
                   cidr: <attacking-ip>/32
       ```
     - Scale up resources temporarily to absorb the attack.  
   - **Fix:**  
     - Enable DDoS protection (e.g., Google Cloud Armor, AWS Shield).  
     - Implement rate-limiting at the ingress level.

---

178. **A namespace deletion operation is stuck. What could cause this issue, and how would you resolve it?**  
   - **Commands to check:**
     - View namespace status:  
       ```bash
       kubectl get namespace <namespace> -o yaml
       ```
     - Identify stuck finalizers:  
       ```bash
       kubectl get namespace <namespace> -o jsonpath='{.spec.finalizers}'
       ```
   - **Fix:**  
     - Remove the finalizers causing the delay:  
       ```bash
       kubectl patch namespace <namespace> -p '{"spec":{"finalizers":[]}}'
       ```

---

179. **Persistent Volumes are not being cleaned up after namespace deletion. How would you debug this issue?**  
   - **Commands to check:**
     - Verify PV reclaim policy:  
       ```bash
       kubectl get pv
       ```
     - Inspect PVC references to the namespace:  
       ```bash
       kubectl describe pvc <pvc-name>
       ```
   - **Fix:**  
     - Update the PV reclaim policy to `Delete` for automatic cleanup:  
       ```yaml
       apiVersion: v1
       kind: PersistentVolume
       spec:
         persistentVolumeReclaimPolicy: Delete
       ```
     - Manually delete orphaned PVs:  
       ```bash
       kubectl delete pv <pv-name>
       ```

---

### **General Use Case Scenarios (180–189)**

180. **How would you debug a pod that is stuck in the Terminating state?**  
   - **Commands to check:**
     - `kubectl get pod <pod-name> -o yaml` to check for finalizers.
     - `kubectl describe pod <pod-name>` to view pending events.
   - **Fix:**
     - Remove stuck finalizers:  
       ```bash
       kubectl patch pod <pod-name> -p '{"metadata":{"finalizers":[]}}'
       ```
     - Investigate stuck processes (e.g., CSI volume detach issues).

181. **A cluster-wide ConfigMap update breaks multiple applications. How would you identify and roll back the changes?**  
   - **Commands to check:**
     - Inspect ConfigMap history:  
       ```bash
       kubectl get cm <config-name> -o yaml
       ```
     - Review application logs for errors.
   - **Fix:**
     - Restore a previous ConfigMap version:  
       ```bash
       kubectl apply -f <previous-config.yaml>
       ```

182. **A RollingUpdate strategy deployment is causing downtime. How would you debug and fix the issue?**  
   - **Commands to check:**
     - Monitor rollout status:  
       ```bash
       kubectl rollout status deployment/<deployment-name>
       ```
     - Inspect pod readiness: `kubectl describe pod`.
   - **Fix:**
     - Pause the rollout and fix the application:  
       ```bash
       kubectl rollout pause deployment/<deployment-name>
       ```

183. **How would you troubleshoot a failed `kubectl apply` operation?**  
   - **Commands to check:**
     - Check YAML for errors using `kubectl apply --dry-run=client -f <file>.yaml`.
     - Review API server logs.
   - **Fix:**
     - Correct invalid fields or syntax errors in the YAML.

184. **A pod is failing due to missing environment variables. How would you debug this?**  
   - **Commands to check:**
     - Inspect pod environment variables:  
       ```bash
       kubectl describe pod <pod-name>
       ```
     - Verify ConfigMap or Secret references.
   - **Fix:** Update the pod spec or the ConfigMap/Secret.

185. **A custom CNI plugin is not initializing correctly. How would you debug and resolve this issue?**  
   - **Commands to check:**
     - Review CNI logs: `journalctl -u kubelet`.
     - Inspect plugin configuration files in `/etc/cni/net.d/`.
   - **Fix:**
     - Correct the plugin’s configuration and ensure compatibility with the Kubernetes version.

186. **A pod's resource requests and limits are misconfigured, causing performance issues. How would you fix this?**  
   - **Commands to check:**
     - View pod resource usage:  
       ```bash
       kubectl top pod <pod-name>
       ```
   - **Fix:**
     - Adjust requests/limits in the pod spec to reflect actual usage:  
       ```yaml
       resources:
         requests:
           cpu: "500m"
           memory: "256Mi"
         limits:
           cpu: "1"
           memory: "512Mi"
       ```

187. **A StatefulSet fails to retain its persistent storage after a cluster upgrade. How would you address this issue?**  
   - **Commands to check:**
     - Inspect PersistentVolumeClaims (PVCs):  
       ```bash
       kubectl get pvc
       ```
   - **Fix:**
     - Verify storage class configurations and reattach volumes to StatefulSet pods.

188. **An application is not scaling as expected with Horizontal Pod Autoscaler. What steps would you take to debug this?**  
   - **Commands to check:**
     - Inspect HPA configuration:  
       ```bash
       kubectl describe hpa <hpa-name>
       ```
     - Verify metrics availability in `kubectl top pod`.
   - **Fix:** Correct HPA thresholds or ensure metric-server is functional.

189. **A pod's liveness probe causes frequent restarts. How would you debug and optimize it?**  
   - **Commands to check:**
     - Review probe configuration in YAML.
     - Inspect probe logs:  
       ```bash
       kubectl logs <pod-name>
       ```
   - **Fix:** Increase probe timeout or adjust the command/script for robustness.

---

### **Extending Kubernetes Troubleshooting (190–200)**

190. **How would you debug a custom resource that fails to reconcile in a custom operator?**  
   - **Commands to check:**
     - Inspect the controller logs:  
       ```bash
       kubectl logs <operator-pod>
       ```
   - **Fix:** Debug the reconcile function in the operator code and reapply the CRD.

191. **A Helm chart installation fails. What steps would you take to debug this?**  
   - **Commands to check:**
     - Dry run the installation:  
       ```bash
       helm install --dry-run --debug <release-name> <chart>
       ```
     - Inspect Kubernetes events.  
   - **Fix:** Resolve values.yaml errors or chart misconfigurations.

192. **A pod using a projected volume fails to start. How would you debug this issue?**  
   - **Commands to check:**
     - Inspect volume configuration in the pod spec.
   - **Fix:** Verify permissions or volume type compatibility.

193. **A cluster upgrade breaks a custom admission controller. How would you debug and resolve this issue?**  
   - **Commands to check:**
     - Review admission controller logs.
     - Test API server webhook communication.  
   - **Fix:** Update the controller to be compatible with the new Kubernetes version.

194. **A service mesh is causing connectivity issues between pods. How would you identify and fix the root cause?**  
   - **Commands to check:**
     - Verify Envoy/sidecar logs.
     - Inspect service mesh configuration for misapplied policies.  
   - **Fix:** Adjust policies or restart mesh components.

195. **A container runtime upgrade fails. What steps would you take to troubleshoot this issue?**  
   - **Commands to check:**
     - Inspect runtime logs (e.g., `containerd` or `dockerd`).
     - Verify configuration files in `/etc/docker/` or `/etc/containerd/`.  
   - **Fix:** Roll back or reinstall the runtime properly.

196. **A pod's readiness probe depends on a remote service that is unavailable. How would you handle this dependency?**  
   - **Commands to check:**
     - Ping the remote service from the pod:  
       ```bash
       kubectl exec <pod-name> -- curl <remote-service-url>
       ```
   - **Fix:** Adjust readiness probe retries or implement fallback logic.

197. **A custom scheduler fails to assign pods to nodes. How would you debug this?**  
   - **Commands to check:**
     - Inspect custom scheduler logs.
     - Check pod events for scheduling errors.  
   - **Fix:** Debug the custom scheduler code or correct pod affinity rules.

198. **A Kubernetes webhook configuration is misbehaving. How would you identify and fix the issue?**  

   - **Commands to check:**
     - Inspect webhook logs and API server logs.
     - Validate webhook certificates and endpoints.  
   - **Fix:** Update the webhook configuration and retry.

199. **A pod using CSI storage is failing to mount the volume. How would you debug this issue?**  

   - **Commands to check:**
     - Inspect CSI driver logs:  
       ```bash
       kubectl logs <csi-driver-pod>
       ```
     - Check pod events for mount errors.  
   - **Fix:** Reapply CSI configurations or debug driver compatibility.

200. **How would you debug a cluster-wide performance degradation issue?**  
   - **Steps:**
     - Check for high resource usage across nodes: `kubectl top node`.
     - Inspect etcd and API server latencies.  
   - **Fix:** Scale the cluster, resolve bottlenecks, or optimize workloads.

---

Good luck!
