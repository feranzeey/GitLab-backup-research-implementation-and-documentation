# GitLab Backup Research & Implementation

## Project Information

**Prepared by:** Oluwaferanmi Dada
**Role:** DevOps Intern
**Project Duration:** June 16th, 2026 - June 29th, 2026
**Project Type:** GitLab Backup Research & Implementation

---

## Repository

GitHub:

https://github.com/feranzeey/GitLab-backup-research-implementation-and-documentation.git

---

# Project Overview

This project focused on researching, implementing, testing, and documenting a GitLab Community Edition backup and recovery solution.

The objective was to create a reliable backup process that protects GitLab application data and configuration files, automates backup execution, applies retention policies, and improves disaster recovery readiness.

The implementation covered:

* GitLab backup validation
* Configuration backup
* Backup automation
* Backup scheduling
* Retention management
* MinIO storage research
* Restore validation preparation
* Documentation and evidence collection

---

# Environment Information

| Component                 | Details                          |
| ------------------------- | -------------------------------- |
| Operating System          | Ubuntu 24.04                     |
| GitLab Edition            | GitLab CE 19.0.2                 |
| Installation Method       | Linux Package Installation (APT) |
| Host Environment          | VirtualBox VM                    |
| Backup Storage            | Local GitLab Backup Directory    |
| Application Backup Path   | `/var/opt/gitlab/backups`        |
| Configuration Backup Path | `/etc/gitlab/config_backup`      |
| Object Storage Research   | MinIO S3-Compatible Storage      |

---

# Project Objectives

The main objectives were:

* Verify GitLab backup functionality
* Test manual application backups
* Test configuration backups
* Configure backup retention
* Automate backup execution
* Configure scheduled backups
* Research MinIO integration
* Validate lifecycle retention
* Prepare disaster recovery procedures

---

# Environment Assessment

The GitLab environment was assessed before making configuration changes.

## Validation Commands

```bash
sudo apt-cache policy gitlab-ce

sudo gitlab-rake gitlab:env:info

sudo gitlab-ctl status

sudo ls -lah /var/opt/gitlab/backups

sudo du -sh /var/opt/gitlab /etc/gitlab
```

Information collected:

* GitLab version
* Installation method
* Service status
* Backup location
* Storage usage

Evidence:

```
evidence/01-gitlab-environment.png
```

---

# Manual Application Backup Validation

A manual GitLab application backup was created.

Command:

```bash
sudo gitlab-backup create
```

Backup location:

```
/var/opt/gitlab/backups
```

Verification:

```bash
sudo ls -lh /var/opt/gitlab/backups
```

Backup archive generated:

```
1782602388_2026_06_27_18.11.6_gitlab_backup.tar
```

Result:

✅ Backup completed successfully
✅ Backup archive created
✅ Backup location verified

Evidence:

```
evidence/02-backup-created.png
```

---

# Configuration Backup Validation

GitLab configuration was backed up separately.

Command:

```bash
sudo gitlab-ctl backup-etc
```

Location:

```
/etc/gitlab/config_backup
```

Important protected files:

* gitlab.rb
* gitlab-secrets.json

Result:

✅ Configuration backup completed successfully

Evidence:

```
evidence/03-config-backup.png
```

---

# Backup Retention Configuration

Local backup retention was configured.

Configuration:

```ruby
gitlab_rails['backup_keep_time'] = 604800
```

Meaning:

```
604800 seconds = 7 days
```

Applied:

```bash
sudo gitlab-ctl reconfigure
```

Verification:

```bash
sudo grep backup_keep_time /etc/gitlab/gitlab.rb
```

Result:

✅ Retention policy configured

Evidence:

```
evidence/04-retention.png
```

---

# Backup Automation

A backup automation script was created.

Location:

```
/usr/local/sbin/gitlab-nightly-backup.sh
```

The script performs:

* Application backup
* Configuration backup
* Backup logging
* Backup verification

Test:

```bash
sudo /usr/local/sbin/gitlab-nightly-backup.sh
```

Result:

✅ Automated backup execution completed

Evidence:

```
evidence/05-backup-script.png
```

---

# Backup Logging

Logs were created for monitoring and troubleshooting.

Location:

```
/var/log/gitlab-backup
```

View logs:

```bash
sudo tail -n 50 /var/log/gitlab-backup/*.log
```

Result:

✅ Backup activities recorded successfully

---

# Scheduled Backup

Daily backup execution was configured using Cron.

Command:

```bash
sudo crontab -e
```

Schedule:

```bash
0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh
```

Verification:

```bash
sudo crontab -l
```

Result:

✅ Daily backup schedule configured

---

# MinIO Integration Research

MinIO was researched as an S3-compatible object storage platform.

Purpose:

* Off-site backup storage
* Disaster recovery support
* Backup redundancy
* Long-term retention

Example:

```bash
mc alias set backup-minio SERVER ACCESS_KEY SECRET_KEY
```

Upload:

```bash
mc cp backup.tar backup-minio/gitlab-backups/daily/
```

Lifecycle example:

```bash
mc ilm rule add backup-minio/gitlab-backups --expire-days 30
```

Result:

✅ MinIO backup and lifecycle process researched

---

# Restore Validation

## Objective

Validate that GitLab backups can be recovered safely.

Restore testing was prepared on a separate staging environment.

Requirements:

* Same GitLab edition
* Same installation method
* Compatible GitLab version

---

# Version Compatibility Issue Identified

During restore preparation, a version mismatch was identified.

## Implementation Environment

```
GitLab CE 19.0.2
```

## Restore Validation Environment

```
GitLab CE 18.11 requirement from project guide
```

Finding:

GitLab restore requires the backup and restore environment to use the same compatible GitLab version.

Resolution:

A compatible GitLab CE 18.11 staging environment was prepared for restore validation.

---

# Restore Validation Status

Restore validation environment prepared and documented.

Final restore execution requires a matching GitLab version staging environment.

Planned validation:

* Admin login verification
* Project visibility check
* Repository access check
* Clone testing
* GitLab service health verification

---

# Challenges Encountered

## GitLab Version Compatibility

Backup version and restore environment versions were different.

Solution:

Prepared a matching version restore environment.

---

## Backup File Permissions

Backup files required permission validation before copying.

Solution:

Checked ownership and access permissions.

---

## Virtual Machine Troubleshooting

Experienced VM restart and network issues during staging setup.

Solution:

Verified VM services, GitLab status, and connectivity.

---

## MinIO Validation

Object storage testing required additional configuration review.

Solution:

Documented upload process and lifecycle management.

---

# Evidence Collected

Evidence includes:

* GitLab environment checks
* Backup creation screenshots
* Configuration backup proof
* Retention configuration
* Automation execution
* Cron scheduling
* Restore preparation

Stored in:

```
evidence/
```

---

# Final Project Status

## Completed

✅ GitLab environment assessment
✅ Application backup testing
✅ Configuration backup testing
✅ Backup automation
✅ Cron scheduling
✅ Backup retention configuration
✅ MinIO research
✅ Documentation completed
✅ Evidence collected

## Restore Validation

Restore environment prepared and documented.

Final restore execution requires a matching GitLab version staging environment.

---

# Conclusion

This project successfully implemented and documented a GitLab backup strategy.

The solution provides:

* Automated backups
* Configuration protection
* Retention management
* Disaster recovery preparation
* Documented recovery procedures

The implementation improves GitLab reliability and reduces the risk of data loss.