# How to Automate (Encrypted) Cloud Backup on Google Drive

There is an open-source utility to interact with Google Drive called [gdrive](https://github.com/prasmussen/gdrive). You can compile from source or download it using a package manager like nix. On the first start, it will output a URL from which you can paste it into the browser to authenticate your identity. You can keep continuing until you get the code and paste it into the terminal.

To find what is in your drive

```bash
gdrive list
```

Since everything is on their server, I don't like to leave any of my personal information unencrypted. The encryption using [gpg](https://gnupg.org/) is widely used so I would recommend it. It is more convenient to archive them into one single file then encrypt it once rather than encrypting them one by one. You might want to compress it also if your internet is slow.

```bash
gdrive delete "my-gdrive-backup-old"

tar czf "my-gdrive-backup" --directory=Cloud .
gpg --encrypt --recipient my@email.addr "my-gdrive-backup"
gdrive upload "my-gdrive-backup.gpg"
```

It is not convenient to manually delete old files every time but luckily we can use their API query parameters to get the list of old files.

```bash
gdrive list -q "name contains '-Cloud.tar.gz.gpg'" | awk 'NR > 1 {print $1}'
```

You can then delete every item in the list one by one.

```bash
old=$(gdrive list -q "name contains '-Cloud.tar.gz.gpg'" | awk 'NR > 1 {print $1}')
for i in $old;
  do gdrive delete $i;
done
```

## My Final Script

```bash
#!/usr/bin/env dash
echo "If the script hangs, exit and check if already authenticated (e.g. by typing gdrive list)."
if ! type gdrive > /dev/null; then
  echo "gdrive not found in \$PATH. Exiting..."
  exit 1
fi

if ! [ -d $MY_CLOUD_DIR ]; then
  echo "Please defines MY_CLOUD_DIR (your cloud folder location) env variable somewhere. Exiting..."
  exit 1
fi

# remove old backups
old=$(gdrive list -q "name contains '-Sync-Cloud.tar.gz.gpg'" | awk 'NR > 1 {print $1}')
for i in $old;
  do gdrive delete $i;
done

cd /tmp
filename="$(date '+%Y_%m_%d')-Sync-Cloud.tar.gz"

echo "Archiving and Compressing ..."
tar czf $filename --directory=$MY_CLOUD_DIR .
gpg --encrypt --recipient tkiat@tutanota.com $filename
rm $filename
gdrive upload "$filename.gpg"
rm "$filename.gpg"
```
