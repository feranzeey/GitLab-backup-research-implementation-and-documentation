# GitLab Backup Research & Implementation

## Project Information

**Prepared by:** Oluwaferanmi Dada  
**Role:** DevOps Intern  
**Project Duration:** June 16th, 2026 - June 29th, 2026  
**Project Type:** GitLab Backup Research & Implementation  

---

# Repository

GitHub Repository:

https://github.com/feranzeey/GitLab-backup-research-implementation-and-documentation.git

---

# Project Overview

This project focused on researching, implementing, testing, and documenting a GitLab backup and recovery solution.

The objective was to create a reliable backup process that protects GitLab application data and configuration files, supports automated backups, verifies backup availability, and documents disaster recovery procedures.

The implementation was performed in a dedicated staging environment using VirtualBox.

---

# Implementation Environment

| Component | Details |
|------------|---------|
| Operating System | Ubuntu 24.04 |
| GitLab Edition | GitLab CE 19.0.2 |
| Installation Method | Linux Package Installation (APT) |
| Host Environment | VirtualBox VM |
| Backup Storage | Local GitLab Backup Directory |
| Object Storage Research | MinIO S3-Compatible Storage |

---

# Environment Setup

A dedicated GitLab staging environment was created using VirtualBox for safe backup testing.

Evidence:

![VirtualBox Environment](evidence/01_environment_setup_virtualbox.png)

---

# GitLab Environment Validation

GitLab environment information was collected to confirm installation details and running components.

Command:

```bash
sudo gitlab-rake gitlab:env:info
```

Validated:

- GitLab version
- GitLab Shell
- Gitaly
- Database information
- Installation details

Evidence:

![GitLab Environment](evidence/02_gitlab_environment_information.png)

---

# GitLab Installation Validation

GitLab Community Edition was installed successfully using the Linux package installation method.

Installation confirmed:

- GitLab package installation
- Required services
- GitLab availability

Evidence:

![GitLab Installation](evidence/03_gitlab_installation_success.png)

---

# Manual Backup Validation

A manual GitLab application backup was created.

Command:

```bash
sudo gitlab-backup create
```

Backup location:

```bash
/var/opt/gitlab/backups
```

Generated backup archive:

```text
1782602388_2026_06_27_18.11.6_gitlab_backup.tar
```

Evidence:

![Backup Execution](evidence/04_gitlab_backup_execution_success.png)

---

# Backup Archive Verification

Backup files were checked after creation.

Command:

```bash
sudo ls -lh /var/opt/gitlab/backups
```

Validation confirmed:

- Backup archive creation
- Backup storage location
- Successful backup generation

Evidence:

![Backup Verification](evidence/05_backup_archive_verification.png)

---

# GitLab Configuration Backup

A configuration backup was created to protect important GitLab settings.

Command:

```bash
sudo gitlab-ctl backup-etc
```

Protected files include:

- `/etc/gitlab/gitlab.rb`
- `/etc/gitlab/gitlab-secrets.json`
- Trusted certificates

Evidence:

![Configuration Backup](evidence/06_gitlab_configuration_backup.png)

---

# Backup Retention Configuration

Backup retention was configured.

Configuration:

```ruby
gitlab_rails['backup_keep_time'] = 604800
```

Meaning:

```
604800 seconds = 7 days
```

Applied with:

```bash
sudo gitlab-ctl reconfigure
```

---

# Backup Automation

A backup automation script was created.

Location:

```bash
/usr/local/sbin/gitlab-nightly-backup.sh
```

The script performs:

- GitLab application backup
- Configuration backup
- Backup logging

---

# Scheduled Backup

Daily automated backups were configured using Cron.

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

---

# Backup Logging

Backup execution logs were configured.

Log location:

```bash
/var/log/gitlab-backup
```

View logs:

```bash
sudo tail -n 50 /var/log/gitlab-backup/*.log
```

---

# MinIO Integration Research

MinIO was researched as an S3-compatible object storage solution for storing backup copies outside the local server.

Benefits:

- Disaster recovery support
- Off-site backup storage
- Backup redundancy
- Lifecycle management

---

# Restore Validation

A staging environment was prepared for restore validation.

Backup tested:

```text
1782602388_2026_06_27_18.11.6_gitlab_backup.tar
```

Restore requirement:

GitLab backups require a compatible GitLab version.

Restore environment:

```text
GitLab CE 18.11
```

Current status:

Restore validation environment prepared and documented.

Final restore execution requires a matching GitLab version staging environment.

---

# Challenges Encountered

- GitLab restore requires matching GitLab versions.
- VirtualBox staging environment required setup and troubleshooting.
- Backup verification required permission troubleshooting.
- MinIO integration required additional research.
- Sensitive GitLab configuration files required secure handling.

---

# Evidence Collected

Evidence included:

- VirtualBox staging environment
- GitLab installation confirmation
- GitLab environment information
- Backup execution
- Backup archive verification
- Configuration backup verification

Evidence location:

```
/evidence
```

---

# Final Project Status

## Completed

✅ GitLab staging VM created  
✅ Ubuntu environment prepared  
✅ GitLab CE installed  
✅ GitLab services verified  
✅ Manual backup tested  
✅ Configuration backup tested  
✅ Backup files verified  
✅ Backup automation documented  
✅ Cron scheduling documented  
✅ Evidence collected  
✅ Documentation completed  


---

# Conclusion

The project successfully implemented and documented a GitLab backup solution covering:

- Backup creation
- Configuration protection
- Backup verification
- Automation
- Retention planning
- Disaster recovery preparation

The completed documentation provides a repeatable backup process suitable for DevOps operations.
