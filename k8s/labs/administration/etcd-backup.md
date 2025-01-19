# Kubernetes ETCD Backup and Restore Tutorial with Automation

## Introduction
This tutorial provides step-by-step instructions to back up, verify, restore, and automate the backup process for the `etcd` database in a Kubernetes cluster. It includes detailed explanations, commands, and automation using shell scripts and cron jobs.

---

## Prerequisites
1. **Kubernetes Cluster**: Ensure the cluster uses `etcd` as the data store.
2. **Access**: Administrative access to the Kubernetes master node.
3. **Required Tools**:
   - `etcdctl`
   - `kubectl`

---

## Install etcdctl on the master node

sudo apt install etcd-client
  
## Step 1: Take an ETCD Backup
Create a snapshot of the `etcd` database to preserve the cluster state for recovery.

### Get the endpoint address using
kubectl -n kube-system exec etcd-master.example.com -- etcdctl member list \\
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \\
  --cert=/etc/kubernetes/pki/etcd/server.crt \\
  --key=/etc/kubernetes/pki/etcd/server.key

### output
a0b86504f30e9756, started, master.example.com, https://172.31.28.251:2380, https://172.31.28.251:2379, false

### assign the advertise_url using export command

```bash
export advertise_url=https://172.31.28.251:2379
```

### Backup Using Explicit Endpoints and Certificates
Use the following command to back up `etcd` securely:

```bash
sudo ETCDCTL_API=3 etcdctl --endpoints $advertise_url \
  --cacert /etc/kubernetes/pki/etcd/ca.crt \
  --cert /etc/kubernetes/pki/etcd/server.crt \
  --key /etc/kubernetes/pki/etcd/server.key \
  snapshot save /tmp/etcd_backup.db
```

#### Explanation:
- `--endpoints $advertise_url`: Specifies the URL of the `etcd` cluster.
- `--cacert`, `--cert`, `--key`: Paths to the certificate authority, client certificate, and client key for secure communication.
- `snapshot save`: Saves the snapshot to `/tmp/etcd_backup.db`.
---

## Step 2: Verify the Backup
Always verify the integrity of the backup.

### Check Backup Status
Run the following command to display details of the backup:

```bash
sudo ETCDCTL_API=3 etcdctl --endpoints $advertise_url \
  --cacert /etc/kubernetes/pki/etcd/ca.crt \
  --cert /etc/kubernetes/pki/etcd/server.crt \
  --key /etc/kubernetes/pki/etcd/server.key \
  --write-out=table snapshot status /tmp/etcd_backup.db
```

#### Explanation:
- `snapshot status`: Displays metadata about the snapshot file.
- `--write-out=table`: Formats the output in a table for better readability.

#### Example Output:
```text
+----------+------------+------------+------------+
|   HASH   |   VERSION  |    SIZE    |  TOTAL KEY |
+----------+------------+------------+------------+
| 123abcd  | 3.5.0      | 5.1 MB     |    3500    |
+----------+------------+------------+------------+
```

---

## Step 3: Restore ETCD Backup
Restoration can be used to recover from failures or set up a new cluster with backed-up data.

### Restore Backup to a New Data Directory
Run the following command:

```bash
sudo ETCDCTL_API=3 etcdctl --endpoints $advertise_url \
  --cacert /etc/kubernetes/pki/etcd/ca.crt \
  --cert /etc/kubernetes/pki/etcd/server.crt \
  --key /etc/kubernetes/pki/etcd/server.key \
  snapshot restore /path/to/backup/etcd_backup.db --data-dir /var/lib/etcd-new
```

#### Explanation:
- `snapshot restore`: Restores the snapshot file.
- `--data-dir`: Specifies the new directory for the restored data (`/var/lib/etcd-new`).

### Update ETCD Configuration
Modify the Kubernetes static pod manifest for `etcd` at `/etc/kubernetes/manifests/etcd.yaml`:
```yaml
- --data-dir=/var/lib/etcd-new
```

### Restart the ETCD Pod
Delete the current `etcd` pod to apply the changes:
```bash
kubectl delete pod -n kube-system etcd-<master-node-name>
```

The kubelet will automatically recreate the pod with the updated configuration.

---

## Step 4: Automate Backups with Cron

### 1. Create a Backup Script
Save the following script as `/opt/etcd-backup.sh`:

```bash
#!/bin/bash
#!/bin/bash
export ETCDCTL_API=3
export ETCDCTL_CACERT=/etc/kubernetes/pki/etcd/ca.crt
export ETCDCTL_CERT=/etc/kubernetes/pki/etcd/server.crt
export ETCDCTL_KEY=/etc/kubernetes/pki/etcd/server.key

BACKUP_DIR="/path/to/backup"
TIMESTAMP=$(date +%Y-%m-%d_%H-%M-%S)
SNAPSHOT="${BACKUP_DIR}/etcd-snapshot-${TIMESTAMP}.db"

# Create a backup
etcdctl snapshot save $SNAPSHOT

# Keep the last 7 backups
find $BACKUP_DIR -type f -name "*.db" -mtime +7 -exec rm {} \;

echo "ETCD backup completed: $SNAPSHOT"
```

### 2. Make the Script Executable and try running once
```bash
chmod +x /opt/etcd-backup.sh
sudo /opt/etcd-backup.sh
```

### 3. Set Up a Cron Job
Edit the cron table:
```bash
crontab -e
```

Add the following line to schedule daily backups at midnight:
```bash
0 0 * * * /opt/etcd-backup.sh >> /var/log/etcd-backup.log 2>&1
```

---

## Step 5: Test Restore (Optional)
1. Use a separate `etcd` instance for testing.
2. Follow the restore steps and validate cluster operations in a staging environment.

---

## Common Issues and Resolutions

| **Issue**                            | **Resolution**                                                                                      |
|--------------------------------------|----------------------------------------------------------------------------------------------------|
| `etcdctl` commands not working       | Ensure `ETCDCTL_API=3` and paths to certificates are correctly set.                               |
| Restore fails with "snapshot file corrupt" | Verify the backup file integrity and repeat the backup process.                                  |
| Cluster health shows unhealthy nodes | Check network connectivity, disk space, and resource utilization on affected nodes.              |

---

## Summary of Commands

| **Command**                              | **Description**                                         |
|------------------------------------------|---------------------------------------------------------|
| `etcdctl snapshot save`                  | Creates a snapshot of the etcd database.               |
| `etcdctl snapshot status`                | Verifies the snapshot file's integrity and details.     |
| `etcdctl snapshot restore`               | Restores the database from a snapshot.                 |
| `kubectl delete pod -n kube-system etcd-<node>` | Restarts the etcd pod to apply changes.               |

---

This guide provides a comprehensive overview of how to back up, verify, restore, and automate `etcd` database management in Kubernetes. Regular testing of the restore process ensures a robust disaster recovery plan.
