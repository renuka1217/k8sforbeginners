### **Kubernetes CronJob Example**

A **CronJob** in Kubernetes is used to schedule and run tasks at specific intervals, similar to `cron` jobs in Linux. Here's an example of a CronJob that runs every minute for one execution cycle.

---

### **CronJob YAML Example**

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: every-minute-cronjob
spec:
  schedule: "*/1 * * * *" # Runs every minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: busybox-cron
            image: busybox
            command: ["sh", "-c", "echo 'Hello from CronJob!'; sleep 30"]
          restartPolicy: Never
```

---

### **Explanation of the YAML**

1. **`apiVersion: batch/v1`:**
   - Specifies that this is a Kubernetes CronJob object.

2. **`kind: CronJob`:**
   - Declares that the object being created is a CronJob.

3. **`metadata`:**
   - **`name: every-minute-cronjob`**: Assigns a unique name to the CronJob.

4. **`spec`:**
   - **`schedule: "*/1 * * * *"`**:
     - Uses cron syntax to specify that the job should run every minute.
     - **Cron Fields:**  
       ```text
       ┌───────────── minute (0 - 59)
       │ ┌───────────── hour (0 - 23)
       │ │ ┌───────────── day of the month (1 - 31)
       │ │ │ ┌───────────── month (1 - 12)
       │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday=0 or 7)
       │ │ │ │ │
       * * * * * 
       ```

   - **`jobTemplate`:** Defines the Job that the CronJob will create for each scheduled execution.

5. **`containers`:**
   - **`name: busybox-cron`**: Names the container.
   - **`image: busybox`**: Uses the lightweight `busybox` image.
   - **`command`:**
     - Runs a simple command that prints a message and sleeps for 30 seconds.

6. **`restartPolicy: Never`:**
   - Ensures the Pod doesn't restart if the container finishes.

---

### **Deploy the CronJob**

1. **Save the YAML File:**
   ```bash
   nano every-minute-cronjob.yaml
   ```
   Copy and paste the YAML content into the file.

2. **Apply the YAML to Create the CronJob:**
   ```bash
   kubectl apply -f every-minute-cronjob.yaml
   ```

---

### **Verify the CronJob**

1. **List CronJobs:**
   ```bash
   kubectl get cronjobs
   ```

   **Example Output:**
   ```
   NAME                   SCHEDULE    SUSPEND   ACTIVE   LAST SCHEDULE   AGE
   every-minute-cronjob   */1 * * * *   False      0        <timestamp>    1m
   ```

2. **List Jobs Created by the CronJob:**
   ```bash
   kubectl get jobs
   ```

   **Example Output:**
   ```
   NAME                           COMPLETIONS   DURATION   AGE
   every-minute-cronjob-xxxxx     1/1           30s        1m
   ```

3. **View Pods Created by the CronJob:**
   ```bash
   kubectl get pods
   ```

4. **View Logs of a Pod:**
   ```bash
   kubectl logs <pod-name>
   ```

   **Expected Output:**
   ```
   Hello from CronJob!
   ```

---

### **Summary**
The CronJob runs a task every minute, creating a Job at the scheduled interval. This task prints "Hello from CronJob!" and sleeps for 30 seconds. CronJobs are useful for periodic tasks such as backups, sending reports, or scheduled maintenance in Kubernetes. The schedule can be adjusted using standard cron syntax to fit various scheduling needs.
