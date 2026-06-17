# Commands Used

## Environment Validation

```bash
sudo apt-cache policy gitlab-ce | sed -n '1,20p'
sudo gitlab-rake gitlab:env:info | sed -n '1,80p'
sudo gitlab-ctl status
sudo ls -lah /var/opt/gitlab/backups
sudo du -sh /var/opt/gitlab /etc/gitlab
sudo grep -n "backup_" /etc/gitlab/gitlab.rb
```

Purpose:

* Verify GitLab version
* Check service status
* Review backup configuration

## Manual Application Backup

```bash
sudo /opt/gitlab/bin/gitlab-backup create
```

Purpose:

* Create a GitLab application backup.

## Verify Backup

```bash
sudo ls -lh /var/opt/gitlab/backups
```

Purpose:

* Confirm backup archive creation.

## Configuration Backup

```bash
sudo gitlab-ctl backup-etc
```

Purpose:

* Back up GitLab configuration files.

## Verify Configuration Backup

```bash
sudo ls -lh /var/backups/gitlab
```

Purpose:

* Confirm configuration backup creation.

## Backup Retention

```bash
sudo grep backup_keep_time /etc/gitlab/gitlab.rb
```

Configured:

```ruby
gitlab_rails['backup_keep_time'] = 604800
```

Purpose:

* Retain backups for 7 days.

## Apply Configuration

```bash
sudo gitlab-ctl reconfigure
```

Purpose:

* Apply GitLab configuration changes.

## Automated Backup Script

```bash
sudo /usr/local/sbin/gitlab-nightly-backup.sh
```

Purpose:

* Execute automated application and configuration backups.

## View Backup Logs

```bash
sudo tail -n 50 $(ls -t /var/log/gitlab-backup/*.log | head -1)
```

Purpose:

* Verify backup execution.

## Schedule Daily Backup

```bash
sudo crontab -e
```

Cron Entry:

```bash
0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh
```

Purpose:

* Run backups daily at 2:00 AM.

## MinIO Upload

```bash
mc alias set backup-minio https://minio.example.com MINIO_ACCESS_KEY MINIO_SECRET_KEY

mc mb --ignore-existing backup-minio/gitlab-backups

latest_app=$(ls -t /var/opt/gitlab/backups/*_gitlab_backup.tar | head -n 1)

mc cp "$latest_app" backup-minio/gitlab-backups/daily/
```

Purpose:

* Upload GitLab backups to MinIO object storage.

## MinIO Lifecycle Policy

```bash
mc ilm rule add backup-minio/gitlab-backups --expire-days 30

mc ilm rule ls backup-minio/gitlab-backups
```

Purpose:

* Automatically remove backup objects older than 30 days.
