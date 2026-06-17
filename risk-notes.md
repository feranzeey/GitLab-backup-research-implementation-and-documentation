# Risk Notes

## Identified Risks

### 1. Local Storage Failure

Risk:

* Local GitLab backups may be lost if the server disk fails.

Mitigation:

* Store backup copies in external object storage such as MinIO.

---

### 2. Restore Validation Not Performed on Production

Risk:

* Production restore testing could cause service disruption and data loss.

Mitigation:

* Restore validation should only be performed on a dedicated staging environment.

---

### 3. Exposure of Sensitive Information

Risk:

* GitLab configuration files contain secrets and credentials.

Mitigation:

* Never include gitlab-secrets.json, tokens, or access keys in screenshots or documentation.

---

### 4. Version Compatibility

Risk:

* Restoring backups to a different GitLab version may fail.

Mitigation:

* Use the same GitLab version and installation method during restore testing.

---

### 5. Object Storage Retention

Risk:

* GitLab local retention settings do not remove backups stored in MinIO.

Mitigation:

* Configure MinIO lifecycle policies to automatically delete expired backups.

---

### 6. Failed Scheduled Backups

Risk:

* Cron jobs may fail silently.

Mitigation:

* Maintain backup logs and periodically review successful execution.
