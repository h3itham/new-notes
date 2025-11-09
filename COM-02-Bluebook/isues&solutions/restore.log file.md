Add the following to crontab 
```bash
0 0 * * 0 truncate -s 0 /var/log/docker_volume_restore.log
```