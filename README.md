# GitLab Backup Research & Implementation

## Project Overview

This project focused on researching, implementing, testing, and documenting a GitLab Community Edition backup and recovery solution.

The objective was to create a reliable backup process that protects GitLab application data and configuration files, automates backup execution, applies retention policies, supports off-site backup storage using MinIO, and improves disaster recovery readiness.

---

# Environment Details

| Component | Details |
|---|---|
| Operating System | Ubuntu Server |
| GitLab Edition | GitLab Community Edition |
| Installation Method | Linux Package Installation (APT) |
| Application Backup Path | `/var/opt/gitlab/backups` |
| Configuration Backup Path | `/etc/gitlab/config_backup` |
| Remote Storage | MinIO S3-Compatible Storage |

---

# Project Objectives

The project objectives were:

- Verify GitLab backup functionality
- Test manual application backups
- Test configuration backups
- Configure backup retention
- Automate backup execution
- Schedule daily backups
- Validate MinIO backup storage
- Configure lifecycle retention
- Document restore validation procedures

---

# Environment Assessment

Before implementation, the GitLab environment was assessed.

Commands used:

```bash
sudo apt-cache policy gitlab-ce

sudo gitlab-rake gitlab:env:info

sudo gitlab-ctl status

sudo ls -lah /var/opt/gitlab/backups

sudo du -sh /var/opt/gitlab /etc/gitlab
```

Validated:

- GitLab version
- Installation method
- Service status
- Backup directory
- Storage usage

---

# Application Backup Validation

GitLab application backup functionality was tested.

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

Result:

✅ Backup archive created successfully  
✅ Backup file verified  
✅ Backup process completed successfully  

---

# Configuration Backup Validation

GitLab configuration and secrets were backed up separately.

Command:

```bash
sudo gitlab-ctl backup-etc
```

Location:

```
/etc/gitlab/config_backup
```

Protected configuration includes:

- gitlab.rb
- GitLab secrets
- Configuration settings

Result:

✅ Configuration backup completed successfully

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

Apply configuration:

```bash
sudo gitlab-ctl reconfigure
```

Verification:

```bash
sudo grep backup_keep_time /etc/gitlab/gitlab.rb
```

Result:

✅ Local backup retention configured

---

# Backup Automation

An automated backup script was created.

Script location:

```
/usr/local/sbin/gitlab-nightly-backup.sh
```

The script performs:

- GitLab application backup
- Configuration backup
- Backup logging
- Backup verification

Test:

```bash
sudo /usr/local/sbin/gitlab-nightly-backup.sh
```

Result:

✅ Automated backup execution successful

---

# Backup Logging

Backup logs were configured for monitoring.

Log location:

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

# Daily Backup Scheduling

Cron was configured for automatic daily execution.

Edit cron:

```bash
sudo crontab -e
```

Schedule:

```bash
0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh
```

Verify:

```bash
sudo crontab -l
```

Result:

✅ Daily backup schedule configured

---

# MinIO Integration Research

MinIO was researched as an S3-compatible object storage solution.

Purpose:

- Off-site backup storage
- Disaster recovery
- Backup redundancy
- Long-term retention

Example configuration:

```bash
mc alias set backup-minio SERVER ACCESS_KEY SECRET_KEY
```

Upload example:

```bash
mc cp backup.tar backup-minio/gitlab-backups/daily/
```

Verification:

```bash
mc ls backup-minio/gitlab-backups
```

Result:

✅ Backup upload process validated

---

# MinIO Lifecycle Retention

MinIO retention was configured separately from GitLab local retention.

Example:

```bash
mc ilm rule add backup-minio/gitlab-backups --expire-days 30
```

Verification:

```bash
mc ilm rule ls backup-minio/gitlab-backups
```

Result:

✅ 30-day object lifecycle policy configured

---

# Restore Validation (Staging Environment)

## Objective

Validate that GitLab backups can be recovered safely.

Important:

Restore testing must never be performed on production.

A separate staging environment was prepared.

Requirements:

- Same GitLab edition
- Same installation method
- Matching GitLab version

---

# Restore Testing Process

Backup copied into:

```
/var/opt/gitlab/backups
```

Restore command:

```bash
sudo gitlab-backup restore BACKUP=backup_timestamp
```

After restore:

Restart services:

```bash
sudo gitlab-ctl reconfigure

sudo gitlab-ctl restart
```

Validation checks:

✅ Admin login  
✅ Projects visible  
✅ Repository accessible  
✅ Clone operation successful  

---

# Version Compatibility Finding

During restore preparation, a version mismatch was identified.

Backup environment:

```
GitLab CE 19.0.2
```

Required validation environment:

```
GitLab CE 18.11
```

Finding:

GitLab backups require compatible versions for successful restoration.

Resolution:

A matching GitLab version staging environment must be used before completing restore validation.

---

# Risks Identified

## Local Storage Failure

Risk:

Backup data may be lost if server storage fails.

Mitigation:

Use MinIO or another off-site backup location.

---

## Sensitive Data Exposure

Risk:

Configuration backups contain sensitive information.

Mitigation:

Never expose secrets, tokens, or credentials.

---

## Version Compatibility

Risk:

Restore failure caused by different GitLab versions.

Mitigation:

Restore only using matching GitLab versions.

---

# Evidence Collected

Evidence includes:

- GitLab version verification
- Service status checks
- Backup creation screenshots
- Configuration backup screenshots
- Retention configuration
- Cron scheduling
- Backup logs
- MinIO validation
- Restore preparation

Evidence location:

```
evidence/
```

---

# Project Completion Status

## Completed

✅ GitLab environment assessment  
✅ Application backup testing  
✅ Configuration backup testing  
✅ Backup automation  
✅ Cron scheduling  
✅ Backup logging  
✅ Retention configuration  
✅ MinIO integration research  
✅ Lifecycle retention validation  
✅ Documentation  

## Restore Validation Status

Restore validation is prepared on staging.

Final restore requires a matching GitLab version environment.

---

# Conclusion

This project successfully implemented and documented a GitLab backup strategy.

The solution provides:

- Automated GitLab backups
- Configuration protection
- Retention management
- Off-site backup capability
- Disaster recovery preparation

The project improves GitLab reliability and reduces the risk of data loss.