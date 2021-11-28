# Google Drive: Let's Automate (Encrypted) Cloud Backup to It

There is an open-source utility to interact with Google Drive called [gdrive](https://github.com/prasmussen/gdrive). On the first start, it will output a URL from which you can paste it into the browser to authenticate your identity.

To find what is in your drive

```bash
gdrive list
```

Since everything is on their server, I don't like to leave any of my personal information unencrypted. The encryption using [gpg](https://gnupg.org/) is widely used so I would recommend it. I also put the check that forces exit in the case of the encryption error. You might want to compress it also if your internet is slow.

```bash
gdrive delete "my-gdrive-backup-old"

tar czf "my-gdrive-backup" --directory=Cloud .
gpg --encrypt --recipient my@email.addr "my-gdrive-backup"
if [[ $? -ne 0 ]]; then echo gpg encryption failed. exiting ...; exit 1; fi;
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
if [[ $? -ne 0 ]]; then echo gpg encryption failed. exiting ...; exit 1; fi;
rm $filename
gdrive upload "$filename.gpg"
rm "$filename.gpg"
```
