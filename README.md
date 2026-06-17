# GitLab Backup Research & Implementation

## Project Overview

This project focuses on researching, implementing, testing, and documenting a reliable backup and recovery solution for GitLab Community Edition running on Ubuntu Linux.

The objective was to create an automated backup strategy that protects GitLab application data and configuration files, supports backup retention policies, and provides a foundation for disaster recovery and business continuity.

---

## Environment Information

* Operating System: Ubuntu 24.04
* GitLab Version: 19.0.2
* Installation Method: Linux Package Installation
* Backup Storage: Local GitLab Backup Directory
* Object Storage: MinIO (S3-Compatible Storage)

---

## Tasks Completed

### Environment Assessment

* Verified GitLab installation and service status.
* Identified backup storage locations.
* Reviewed existing GitLab backup configuration.
* Collected environment and storage information.

### Application Backup Validation

* Successfully executed manual GitLab application backups.
* Verified backup archive creation.
* Recorded backup output and backup locations.

### Configuration Backup Validation

* Executed GitLab configuration backups using:

```bash
gitlab-ctl backup-etc
```

* Verified configuration backup archive creation.
* Documented configuration and secret file requirements.

### Backup Retention Configuration

Configured GitLab backup retention:

```ruby
gitlab_rails['backup_keep_time'] = 604800
```

Retention period:

* 7 Days

Applied configuration successfully using:

```bash
gitlab-ctl reconfigure
```

### Backup Automation

Created a reusable backup automation script to:

* Execute GitLab application backups
* Execute configuration backups
* Generate backup logs
* Record backup execution details

### Scheduled Backups

Configured daily automated backups using cron:

```bash
0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh
```

Backup Schedule:

* Daily at 2:00 AM

### Backup Logging

Implemented logging for backup operations.

Logs are stored in:

```bash
/var/log/gitlab-backup/
```

This provides:

* Backup execution records
* Error tracking
* Troubleshooting information

### MinIO Integration Research

Researched and documented:

* S3-compatible backup storage
* MinIO connectivity requirements
* Bucket management
* Security considerations
* Object storage backup workflow

### MinIO Upload Validation

Installed and configured MinIO Client (mc).

Validated:

* Bucket creation
* Backup uploads
* Object verification

Successfully tested uploading GitLab backup archives to MinIO storage.

### MinIO Lifecycle Management

Configured object lifecycle management policy:

* Retention Period: 30 Days

Verified lifecycle rules using:

```bash
mc ilm rule ls backup-minio/gitlab-backups
```

---

## Backup Components Protected

### Application Backups

Stored in:

```bash
/var/opt/gitlab/backups
```

Contains:

* Repositories
* Database
* Issues
* Merge Requests
* Wikis
* CI/CD Metadata

### Configuration Backups

Generated using:

```bash
gitlab-ctl backup-etc
```

Includes:

* gitlab.rb
* gitlab-secrets.json

---

## Evidence Collected

The project includes evidence demonstrating:

* Successful backup execution
* Configuration backup creation
* Retention configuration
* Automated scheduling
* Backup log generation
* MinIO upload validation
* Lifecycle rule configuration

Evidence files are stored in the `evidence/` directory.

---

## Current Status

Completed:

* Environment validation
* Application backup testing
* Configuration backup testing
* Retention implementation
* Backup automation
* Scheduled backup configuration
* MinIO integration research
* MinIO upload validation
* MinIO lifecycle configuration

Pending:

* Full restore validation on a separate staging GitLab environment

---

## Key Outcome

A documented and tested GitLab backup solution has been implemented, providing automated daily backups, backup retention management, object storage integration, and recovery documentation to improve data protection and operational resilience.

## Challenges Encountered

- GitLab version in the environment (19.0.2) differed from the version referenced in the research guide.
- Backup log verification required troubleshooting command syntax.
- MinIO testing was performed using a lab environment and placeholder configuration.
- Restore validation requires a separate staging environment and was not performed on production.