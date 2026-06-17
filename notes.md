
---

# notes.md

```markdown
# Research Notes

## Environment Information

Operating System:
- Ubuntu 24.04

GitLab Version:
- GitLab CE 19.0.2

Installation Method:
- Linux Package Installation

---

## Backup Locations

Application Backups:

/var/opt/gitlab/backups

Configuration Backups:

/etc/gitlab/config_backup

Log Files:

/var/log/gitlab-backup

---

## Backup Components

Application Backup Includes:

- Git repositories
- PostgreSQL database
- Merge requests
- Issues
- Wikis
- CI/CD metadata

Configuration Backup Includes:

- gitlab.rb
- gitlab-secrets.json

---

## Manual Backup Testing

Command Used:

sudo /opt/gitlab/bin/gitlab-backup create

Result:

- Backup completed successfully
- Backup archive created successfully
- Backup file verified in backup directory

---

## Configuration Backup Testing

Command Used:

sudo gitlab-ctl backup-etc

Result:

- Configuration archive created successfully
- Backup verified in configuration backup directory

---

## Retention Configuration

Configured:

gitlab_rails['backup_keep_time'] = 604800

Meaning:

- Backup retention period = 7 Days

Verification:

sudo grep backup_keep_time /etc/gitlab/gitlab.rb

Result:

- Retention configuration applied successfully

---

## Automated Backup Script

Location:

/usr/local/sbin/gitlab-nightly-backup.sh

Purpose:

- Execute application backups
- Execute configuration backups
- Generate backup logs
- Simplify backup operations

Result:

- Script executed successfully

---

## Cron Configuration

Schedule:

0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh

Purpose:

- Execute backups automatically every day at 2:00 AM

Verification:

sudo crontab -l

Result:

- Cron job configured successfully

---

## MinIO Research Findings

MinIO provides:

- S3-compatible object storage
- Off-site backup storage
- Lifecycle management
- Additional protection against local storage failure

Benefits:

- Improved disaster recovery
- Additional backup redundancy
- Centralized backup management

---

## MinIO Upload Testing

Activities Completed:

- MinIO Client installation
- Bucket creation
- Backup upload testing
- Object verification

Result:

- Upload completed successfully

---

## Lifecycle Policy

Configuration:

30-Day Retention Policy

Command:

mc ilm rule add backup-minio/gitlab-backups --expire-days 30

Purpose:

- Automatically remove expired backup objects

Result:

- Lifecycle rule verified successfully

---

## Restore Validation Notes

Restore testing should only be performed on a dedicated staging environment.

Requirements:

- Separate server
- Matching GitLab version
- Same installation method

Validation Checklist:

- Admin login successful
- Projects visible
- Repositories accessible
- Clone operation successful

Current Status:

Restore procedure documented.
Awaiting confirmation on staging environment requirement.