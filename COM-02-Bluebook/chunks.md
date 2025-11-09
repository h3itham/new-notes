```bash
0 1 * * * find /var/lib/docker/volumes/bluebook-prod-media/_data/temp_chunks -name "blob_*" -exec rm -fv {} \;
```