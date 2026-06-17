# GitLab 18.11 CE Backup Research Notes

## Phase 8: MinIO Integration Research

### Objective

Research how GitLab CE 18.11 backups can be stored in MinIO (S3-compatible object storage) and document the required configuration.

### Research Findings

* GitLab supports remote backup uploads to S3-compatible object storage.
* MinIO is compatible with the S3 API and can be used as a backup destination.
* GitLab creates backups locally before uploading them to remote storage.
* Remote retention is not managed by GitLab local retention settings.
* MinIO lifecycle rules (ILM) should be used to automatically delete old backup objects.
* Real access keys and secrets must never be stored in documentation.

### Example GitLab MinIO Configuration

```ruby
gitlab_rails['backup_upload_connection'] = {
  'provider' => 'AWS',
  'region' => 'us-east-1',
  'aws_access_key_id' => 'MINIO_ACCESS_KEY',
  'aws_secret_access_key' => 'MINIO_SECRET_KEY',
  'endpoint' => 'https://minio.example.com',
  'path_style' => true,
  'aws_signature_version' => 4
}

gitlab_rails['backup_upload_remote_directory'] = 'gitlab-backups'
```

### Information Required Before Implementation

| Item           | Status                        |
| -------------- | ----------------------------- |
| MinIO Endpoint | Pending reviewer confirmation |
| Bucket Name    | Pending reviewer confirmation |
| Retention Days | Pending reviewer confirmation |
| Access Policy  | Pending reviewer confirmation |
| Access Key     | Not provided                  |
| Secret Key     | Not provided                  |

### Planned Validation Steps

1. Confirm MinIO endpoint.
2. Confirm backup bucket name.
3. Confirm retention requirements.
4. Configure GitLab backup upload settings.
5. Run a manual backup.
6. Verify backup object appears in the MinIO bucket.
7. Configure MinIO lifecycle retention policy.
8. Document results and screenshots.

### Current Status

Research completed.

MinIO integration cannot be fully tested at this time because the following information has not been provided:

* MinIO endpoint
* Bucket name
* Access credentials
* Retention policy requirements

Awaiting reviewer approval and MinIO configuration details before implementation.

### Notes

Do not store real credentials in documentation.

Use placeholders such as:

* MINIO_ACCESS_KEY
* MINIO_SECRET_KEY

All screenshots and evidence should be sanitized before submission.

## Environment Baseline

Operating System: Ubuntu 24.04

GitLab Version: 19.0.2

GitLab Installation Type: Linux Package Installation

Backup Path: /var/opt/gitlab/backups

Configuration Backup Path: /etc/gitlab/config_backup

## Daily Backup Schedule

Cron Entry:

0 2 * * * /usr/local/sbin/gitlab-nightly-backup.sh

Status: Configured and verified.

## MinIO Research Status

Research completed.

MinIO client installation was attempted but remote object storage testing could not be completed because no production-approved MinIO endpoint, bucket name, access key, or secret key were provided.

Current status:

* MinIO endpoint: Pending
* Bucket name: Pending
* Retention policy: Pending
* Credentials: Pending

Recommendation:

Proceed with local GitLab backups and retention policy implementation while awaiting reviewer-approved MinIO details.

# GitLab Backup Research Notes

## Phase 2 - Environment Information

### Operating System

Ubuntu 22.04.5 LTS (Jammy Jellyfish)

### Hostname

gitlab-staging

### GitLab Version

Not installed / unable to determine

### Backup Path

N/A

### Configuration Backup Path

N/A

### GitLab Disk Usage

N/A

### GitLab Configuration Usage

N/A

## Findings

The following checks indicate that GitLab is not installed on this VM:

* `gitlab-rake` command not found
* `gitlab-ctl` command not found
* `/etc/gitlab` directory does not exist
* `/var/opt/gitlab` directory does not exist
* `gitlab-ce` package cannot be located via apt
* GitLab backup directories are absent

## Impact

Because GitLab is not installed, the following phases cannot be completed on this VM:

* Phase 3 – Manual Backup Test
* Phase 4 – Backup Configuration Files
* Phase 5 – Configure Retention
* Phase 6 – Create Backup Script
* Phase 7 – Schedule Daily Backup
* Phase 9 – Test MinIO Upload
* Phase 10 – Configure MinIO Retention
* Phase 11 – Restore Validation

All GitLab-specific commands will fail until GitLab CE is installed.

## Additional Validation Performed

Commands executed:

```bash
sudo apt-cache policy gitlab-ce
sudo find / -maxdepth 3 -iname "*gitlab*" 2>/dev/null
dpkg -l | grep -i gitlab
```

Result:

* No GitLab package identified
* No GitLab installation directories identified
* No GitLab services available

## Recommendation

Install GitLab CE on the VM or obtain access to the correct GitLab staging server before continuing with backup implementation and validation activities.
