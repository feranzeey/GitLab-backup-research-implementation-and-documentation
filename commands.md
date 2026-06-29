# GitLab Backup Research & Implementation - Commands Reference

## 1. Environment Information

### Check Operating System

```bash
lsb_release -a
```

---

### Check GitLab Version

```bash
sudo gitlab-rake gitlab:env:info
```

Used to verify:

* GitLab Edition
* GitLab Version
* GitLab components

---

### Check GitLab Services

```bash
sudo gitlab-ctl status
```

Expected services:

* nginx
* postgresql
* redis
* sidekiq
* puma

---

# 2. GitLab Installation Validation

### Check GitLab Package

```bash
sudo apt-cache policy gitlab-ce
```

---

### Check GitLab Configuration

```bash
sudo gitlab-ctl show-config
```

---

# 3. Application Backup Commands

## Create GitLab Backup

```bash
sudo /opt/gitlab/bin/gitlab-backup create
```

or:

```bash
sudo gitlab-backup create
```

Backup location:

```bash
/var/opt/gitlab/backups
```

---

## Verify Backup Files

```bash
sudo ls -lh /var/opt/gitlab/backups
```

Example backup:

```text
1782602388_2026_06_27_18.11.6_gitlab_backup.tar
```

---

# 4. Configuration Backup Commands

## Create Configuration Backup

```bash
sudo gitlab-ctl backup-etc
```

Configuration backup location:

```bash
/etc/gitlab/config_backup
```

---

## Verify Configuration Backup

```bash
sudo ls -lh /etc/gitlab/config_backup
```

---

# 5. Backup Retention Configuration

Edit GitLab configuration:

```bash
sudo nano /etc/gitlab/gitlab.rb
```

Add:

```ruby
gitlab_rails['backup_keep_time'] = 604800
```

Meaning:

```
604800 seconds = 7 days
```

Apply changes:

```bash
sudo gitlab-ctl reconfigure
```

Verify:

```bash
sudo grep backup_keep_time /etc/gitlab/gitlab.rb
```

---

# 6. Automated Backup Script

Create script:

```bash
sudo nano /usr/local/sbin/gitlab-nightly-backup.sh
```

Make executable:

```bash
sudo chmod +x /usr/local/sbin/gitlab-nightly-backup.sh
```

Run manually:

```bash
sudo /usr/local/sbin/gitlab-nightly-backup.sh
```

---

# 7. Backup Logging

Create log directory:

```bash
sudo mkdir -p /var/log/gitlab-backup
```

View logs:

```bash
sudo ls -lah /var/log/gitlab-backup
```

View latest backup log:

```bash
sudo tail -n 50 /var/log/gitlab-backup/*.log
```

---

# 8. Cron Scheduled Backup

Open root cron:

```bash
sudo crontab -e
```

Add:

```bash
0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh
```

Meaning:

```
Run backup every day at 2:00 AM
```

Verify:

```bash
sudo crontab -l
```

---

# 9. MinIO Backup Research Commands

## Configure MinIO Client

```bash
mc alias set backup-minio https://minio.example.com ACCESS_KEY SECRET_KEY
```

---

## Create Backup Bucket

```bash
mc mb backup-minio/gitlab-backups
```

---

## Upload Backup

Example:

```bash
mc cp /var/opt/gitlab/backups/*.tar backup-minio/gitlab-backups/
```

---

## Verify Upload

```bash
mc ls backup-minio/gitlab-backups/
```

---

# 10. MinIO Lifecycle Management

Create 30-day retention:

```bash
mc ilm rule add backup-minio/gitlab-backups --expire-days 30
```

Check lifecycle:

```bash
mc ilm rule ls backup-minio/gitlab-backups
```

---

# 11. Restore Validation Commands

## Stop GitLab Before Restore

```bash
sudo gitlab-ctl stop
```

---

## Move Backup File

```bash
sudo cp backup_file.tar /var/opt/gitlab/backups/
```

---

## Set Permissions

```bash
sudo chown git:git /var/opt/gitlab/backups/*.tar
```

---

## Restore Backup

Example:

```bash
sudo gitlab-backup restore BACKUP=1782602388_2026_06_27_18.11.6
```

---

## Reconfigure GitLab

```bash
sudo gitlab-ctl reconfigure
```

---

## Restart GitLab

```bash
sudo gitlab-ctl restart
```

---

# 12. Restore Validation Checks

Check services:

```bash
sudo gitlab-ctl status
```

Check GitLab health:

```bash
sudo gitlab-rake gitlab:check
```

Validate:

* Admin login
* Projects visible
* Repository access
* Clone operation

---

# 13. Troubleshooting Commands

## Check GitLab Logs

```bash
sudo gitlab-ctl tail
```

---

## Restart GitLab

```bash
sudo gitlab-ctl restart
```

---

## Check Disk Usage

```bash
df -h
```

---

## Check GitLab Storage

```bash
sudo du -sh /var/opt/gitlab
```

---

# 14. Useful Evidence Commands

Backup verification:

```bash
sudo ls -lh /var/opt/gitlab/backups
```

Configuration verification:

```bash
sudo ls -lh /etc/gitlab/config_backup
```

Service verification:

```bash
sudo gitlab-ctl status
```

Cron verification:

```bash
sudo crontab -l
```

---

# Project Summary

These commands were used during the GitLab Backup Research & Implementation project to:

* Validate GitLab environment
* Create application backups
* Create configuration backups
* Configure retention
* Automate backups
* Schedule backups
* Research MinIO storage
* Prepare restore validation
* Troubleshoot issues
