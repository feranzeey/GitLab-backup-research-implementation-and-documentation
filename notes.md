# GitLab Backup Research & Implementation Notes

## 1. Environment Information

### Infrastructure Details

| Component           | Details                            |
| ------------------- | ---------------------------------- |
| Operating System    | Ubuntu 24.04                       |
| GitLab Edition      | GitLab CE 19.0.2                   |
| Installation Method | Linux Package Installation (APT)   |
| Host Environment    | VirtualBox Virtual Machine         |
| Backup Storage      | Local GitLab Backup Directory      |
| Backup Type         | Application + Configuration Backup |

---

# 2. Project Repository

GitHub Repository:

https://github.com/feranzeey/GitLab-backup-research-implementation-and-documentation.git

---

# 3. Backup Locations

## Application Backup Location

```
/var/opt/gitlab/backups
```

Contains GitLab application data including:

* Git repositories
* PostgreSQL database
* Merge requests
* Issues
* Wikis
* CI/CD metadata
* Uploaded files

---

## Configuration Backup Location

```
/etc/gitlab/config_backup
```

Contains important GitLab configuration files:

* gitlab.rb
* gitlab-secrets.json
* Trusted certificates

---

## Backup Logs Location

```
/var/log/gitlab-backup
```

Used for:

* Backup execution logs
* Troubleshooting
* Monitoring backup activity

---

# 4. Environment Validation

The GitLab environment was checked before backup implementation.

Commands used:

```bash
sudo gitlab-rake gitlab:env:info

sudo gitlab-ctl status

sudo ls -lh /var/opt/gitlab/backups
```

Validation confirmed:

* GitLab services running successfully
* GitLab installation operational
* Backup directory available

```

---

# 5. Manual Application Backup Testing

A manual GitLab backup was created to verify backup functionality.

Command:

```bash
sudo /opt/gitlab/bin/gitlab-backup create
```

Backup output generated:

```
1782602388_2026_06_27_18.11.6_gitlab_backup.tar
```

Backup location:

```
/var/opt/gitlab/backups
```

Verification:

```bash
sudo ls -lh /var/opt/gitlab/backups
```

Result:

* Backup completed successfully
* Backup archive created
* Backup file verified

```

---

# 6. Configuration Backup Testing

GitLab configuration backup was created.

Command:

```bash
sudo gitlab-ctl backup-etc
```

Generated archive:

```
/etc/gitlab/config_backup/gitlab_config_*.tar
```

Protected files:

* gitlab.rb
* gitlab-secrets.json

Verification:

```bash
sudo ls -lh /etc/gitlab/config_backup
```

Result:

* Configuration backup completed successfully

```

---

# 7. Backup Retention Configuration

Backup retention was configured to automatically remove older backups.

Configuration:

```ruby
gitlab_rails['backup_keep_time'] = 604800
```

Retention period:

```
604800 seconds = 7 days
```

Applied using:

```bash
sudo gitlab-ctl reconfigure
```

Verification:

```bash
sudo grep backup_keep_time /etc/gitlab/gitlab.rb
```

Result:

* Retention policy applied successfully

---

# 8. Automated Backup Implementation

Created automated backup script:

Location:

```
/usr/local/sbin/gitlab-nightly-backup.sh
```

Purpose:

* Run GitLab application backups
* Run configuration backups
* Generate backup logs
* Simplify backup operations

Script execution:

```bash
sudo /usr/local/sbin/gitlab-nightly-backup.sh
```

Result:

* Backup automation tested successfully

---

# 9. Cron Scheduled Backup

Daily backup scheduling was configured.

Cron entry:

```bash
0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh
```

Meaning:

Backup runs every day at 2:00 AM.

Verification:

```bash
sudo crontab -l
```

Result:

* Scheduled backup configured successfully

```

---

# 10. MinIO Object Storage Research

MinIO was researched as an S3-compatible storage solution for external backup storage.

Benefits:

* Off-site backup storage
* Disaster recovery support
* Backup redundancy
* Lifecycle management
* Protection against local storage failure

---

# 11. MinIO Upload Testing

Activities researched:

* MinIO client setup
* Bucket creation
* Backup upload
* Object verification

Example upload:

```bash
mc cp backup-file.tar backup-minio/gitlab-backups/
```

Verification:

```bash
mc ls backup-minio/gitlab-backups/
```

Result:

* Upload process validated

---

# 12. MinIO Lifecycle Management

Lifecycle policy:

```
30-Day Backup Retention
```

Command:

```bash
mc ilm rule add backup-minio/gitlab-backups --expire-days 30
```

Purpose:

Automatically remove expired backup objects.

Verification:

```bash
mc ilm rule ls backup-minio/gitlab-backups
```

Result:

* Lifecycle management configured successfully

---

# 13. Restore Validation

## Objective

Validate that GitLab backups can be restored safely in a staging environment.

---

## Restore Environment

Prepared staging environment:

* VirtualBox VM
* GitLab CE installation
* Isolated testing environment

Restore requirement:

GitLab backup restoration requires a matching GitLab version.

---

## Backup Tested

Backup archive:

```
1782602388_2026_06_27_18.11.6_gitlab_backup.tar
```

---

## Restore Validation Finding

Backup version:

```
GitLab CE 19.0.2
```

Available staging version:

```
GitLab CE 18.11
```

Finding:

Restore execution requires a matching GitLab version staging environment.

Current status:

* Restore procedure documented
* Staging environment prepared
* Version compatibility identified

---

# 14. Evidence Collected

The following evidence was captured:

```
evidence/
│
├── environment-validation.png
├── gitlab-installation.png
├── gitlab-status.png
├── application-backup-success.png
├── configuration-backup.png
├── cron-verification.png
└── backup-directory.png
```

Evidence demonstrates:

* GitLab installation
* Service verification
* Backup creation
* Configuration backup
* Automation setup
* Backup verification

---

# 15. Project Outcome

Completed:

✔ GitLab staging environment setup
✔ GitLab CE installation
✔ Environment assessment
✔ Application backup testing
✔ Configuration backup testing
✔ Backup retention configuration
✔ Automated backup scripting
✔ Cron scheduling
✔ MinIO backup research
✔ Restore validation documentation

Final result:

A documented GitLab backup solution was implemented with automation, verification, retention planning, and disaster recovery considerations.
