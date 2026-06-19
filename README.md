# GitLab Backup Research & Implementation

## Project Overview

This project focused on researching, implementing, testing, and documenting a GitLab backup and recovery solution.

The objective was to create a reliable backup process that protects GitLab application data and configuration files, automates backup execution, enforces retention policies, and supports disaster recovery planning.

---

## Environment Information

| Component | Details |
|------------|------------|
| Operating System | Ubuntu 24.04 |
| GitLab Version | GitLab CE 19.0.2 |
| Installation Method | Linux Package Installation |
| Backup Storage | Local GitLab Backup Directory |
| Object Storage | MinIO (S3-Compatible Storage) |

---

## Project Objectives

- Verify GitLab backup functionality
- Test manual application backups
- Test configuration backups
- Configure backup retention
- Automate backup execution
- Configure scheduled backups
- Research and validate MinIO integration
- Configure MinIO lifecycle policies
- Document backup and recovery procedures

---

# Environment Assessment

The GitLab environment was assessed to gather baseline information before making any configuration changes.

### Validation Commands

```bash
sudo apt-cache policy gitlab-ce | sed -n '1,20p'
sudo gitlab-rake gitlab:env:info | sed -n '1,80p'
sudo gitlab-ctl status
sudo ls -lah /var/opt/gitlab/backups
sudo du -sh /var/opt/gitlab /etc/gitlab
sudo grep -n "backup_" /etc/gitlab/gitlab.rb
```

### Information Collected

- GitLab version
- Operating system details
- Backup directory location
- Existing backup configuration
- Service status
- Disk utilization

---

# Manual Backup Validation

A manual GitLab application backup was executed to verify backup functionality.

### Command Used

```bash
sudo /opt/gitlab/bin/gitlab-backup create
```

### Verification

```bash
sudo ls -lh /var/opt/gitlab/backups
```

### Result

- Backup completed successfully
- Backup archive created successfully
- Backup file verified in backup directory

---

# Configuration Backup Validation

GitLab configuration backups were created to ensure critical settings and secrets could be recovered.

### Command Used

```bash
sudo gitlab-ctl backup-etc
```

### Verification

```bash
sudo ls -lh /etc/gitlab/config_backup
```

### Result

- Configuration backup completed successfully
- Configuration archive verified

### Important Files Protected

- gitlab.rb
- gitlab-secrets.json

---

# Backup Retention Configuration

Configured local backup retention using GitLab settings.

### Configuration

```ruby
gitlab_rails['backup_keep_time'] = 604800
```

### Meaning

604800 seconds = 7 days

### Apply Configuration

```bash
sudo gitlab-ctl reconfigure
```

### Verification

```bash
sudo grep backup_keep_time /etc/gitlab/gitlab.rb
```

### Result

Retention policy successfully configured.

---

# Backup Automation

An automated backup script was created to perform application backups, configuration backups, and logging.

### Script Location

```bash
/usr/local/sbin/gitlab-nightly-backup.sh
```

### Backup Script

```bash
#!/usr/bin/env bash

set -euo pipefail

LOG_DIR="/var/log/gitlab-backup"

mkdir -p "$LOG_DIR"

STAMP=$(date +%Y%m%d-%H%M%S)

LOG_FILE="$LOG_DIR/backup-$STAMP.log"

echo "Starting backup..." >> "$LOG_FILE"

sudo /opt/gitlab/bin/gitlab-backup create CRON=1 >> "$LOG_FILE" 2>&1

sudo gitlab-ctl backup-etc >> "$LOG_FILE" 2>&1

echo "Finished backup." >> "$LOG_FILE"
```

### Make Executable

```bash
sudo chmod +x /usr/local/sbin/gitlab-nightly-backup.sh
```

### Test Execution

```bash
sudo /usr/local/sbin/gitlab-nightly-backup.sh
```

### Result

Backup automation executed successfully.

---

# Backup Logging

Backup execution logs were generated to support monitoring and troubleshooting.

### Log Location

```bash
/var/log/gitlab-backup
```

### View Latest Log

```bash
sudo tail -n 50 $(ls -t /var/log/gitlab-backup/*.log | head -1)
```

### Result

Backup activity successfully recorded.

---

# Scheduled Backups

Daily automated backups were configured using Cron.

### Edit Cron

```bash
sudo crontab -e
```

### Schedule

```bash
0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh
```

### Verification

```bash
sudo crontab -l
```

### Result

Daily backups scheduled for 2:00 AM.

---

# MinIO Integration Research

MinIO was researched as an S3-compatible object storage platform for storing off-server backup copies.

### Benefits

- Disaster recovery support
- Backup redundancy
- Off-site backup storage
- Lifecycle management
- Improved backup resilience

---

# MinIO Upload Testing

MinIO client commands were reviewed and tested using example configurations.

### Configure MinIO

```bash
mc alias set backup-minio https://minio.example.com MINIO_ACCESS_KEY MINIO_SECRET_KEY
```

### Create Bucket

```bash
mc mb --ignore-existing backup-minio/gitlab-backups
```

### Upload Latest Backup

```bash
latest_app=$(ls -t /var/opt/gitlab/backups/*_gitlab_backup.tar | head -n 1)

mc cp "$latest_app" backup-minio/gitlab-backups/daily/
```

### Verify Upload

```bash
mc ls backup-minio/gitlab-backups/daily/
```

### Result

Backup upload process validated successfully.

---

# MinIO Lifecycle Management

A lifecycle policy was configured to automatically remove old backup objects.

### Configure Lifecycle Rule

```bash
mc ilm rule add backup-minio/gitlab-backups --expire-days 30
```

### Verify Lifecycle Rule

```bash
mc ilm rule ls backup-minio/gitlab-backups
```

### Result

30-day retention policy configured successfully.

---

# Risks Identified

## Local Storage Failure

Risk:
- Local backups may be lost if the server storage fails.

Mitigation:
- Maintain backup copies in MinIO object storage.

---

## Sensitive Data Exposure

Risk:
- Configuration files may contain secrets and credentials.

Mitigation:
- Never expose secrets, access keys, or tokens in documentation or screenshots.

---

## Restore Validation

Risk:
- Production restore testing could impact service availability.

Mitigation:
- Restore validation should only be performed on a dedicated staging environment.

---

## Version Compatibility

Risk:
- Backup restoration may fail when GitLab versions differ.

Mitigation:
- Use the same GitLab version and installation method for restore testing.

---

# Challenges Encountered

- GitLab version in the environment (19.0.2) differed from the version referenced in the research guide.
- Backup log verification required troubleshooting command syntax.
- MinIO testing required validation of lifecycle and retention configuration.
- Restore validation requires a dedicated staging environment.

---

# Evidence Collected

The following evidence was collected throughout the implementation:

- Environment validation screenshots
- GitLab version verification
- Backup creation screenshots
- Configuration backup screenshots
- Retention configuration screenshots
- Automated backup execution logs
- Cron schedule verification
- MinIO upload validation
- MinIO lifecycle policy verification

All screenshots are stored in the `evidence/` directory.

---

# Current Status

# GitLab Backup Research & Implementation

## Status

**In Progress**

### Work Completed

* Created and configured a dedicated GitLab staging virtual machine in VirtualBox.
* Installed Ubuntu Server and applied system updates.
* Installed required GitLab dependencies.
* Installed and configured GitLab Community Edition on the staging environment.
* Verified GitLab services were operational using `gitlab-ctl status`.
* Verified existing production backup files and confirmed multiple valid GitLab backup archives were available.
* Researched GitLab backup and restore procedures, including version compatibility requirements.
* Identified that available backup archives were created using GitLab CE 19.0.2.
* Built a staging environment specifically for restore validation and disaster recovery testing.
* Updated project documentation and uploaded implementation evidence to GitHub.

### Validation Results

| Validation Item                | Status |
| ------------------------------ | ------ |
| Staging VM Creation            | PASS   |
| Ubuntu Installation & Updates  | PASS   |
| GitLab Dependency Installation | PASS   |
| GitLab CE Installation         | PASS   |
| GitLab Service Verification    | PASS   |
| Backup Verification            | PASS   |
| Documentation Update           | PASS   |

### Restore Validation Findings

Restore testing was initiated using a dedicated staging environment. During validation, GitLab backup compatibility requirements were reviewed and a version mismatch was identified.

| Component                   | Version           |
| --------------------------- | ----------------- |
| Backup Version              | GitLab CE 19.0.2  |
| Staging Environment Version | GitLab CE 18.11.x |

GitLab backups can only be restored to a GitLab instance running the same version (or a compatible upgrade path). Because the staging environment version does not match the backup version, restore validation cannot proceed until the staging environment is upgraded to GitLab CE 19.0.2.

### Current Status

The GitLab staging environment has been successfully deployed and verified. Backup files have been confirmed and restore requirements have been researched. Restore validation is currently blocked by a GitLab version mismatch between the backup archive and the staging environment.

### Next Steps

1. Upgrade the staging GitLab instance to GitLab CE 19.0.2.
2. Verify GitLab services after the upgrade.
3. Copy a backup archive into the staging environment.
4. Perform a full backup restoration test.
5. Validate user access, projects, repositories, and application functionality.
6. Capture restore-validation evidence and update project documentation.

### Project Progress

| Task                               | Status                     |
| ---------------------------------- | -------------------------- |
| Environment Assessment             | PASS                       |
| Manual Application Backup          | PASS                       |
| Configuration Backup               | PASS                       |
| Backup Retention Configuration     | PASS                       |
| Backup Automation                  | PASS                       |
| Backup Logging                     | PASS                       |
| Scheduled Backups                  | PASS                       |
| MinIO Research                     | PASS                       |
| MinIO Upload Validation            | PASS                       |
| MinIO Lifecycle Policy             | PASS                       |
| Staging Environment Setup          | PASS                       |
| Restore Validation                 | BLOCKED (Version Mismatch) |
| Final Disaster Recovery Validation | PENDING                    |
