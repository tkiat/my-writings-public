# Google Drive: Let's Automate (Encrypted) Cloud Backup to It

I strongly discourage anyone from storing data on someone else's server as they can exploit your (private) data. The situation is different if you encrypt your data before uploading e.g. with the widely used [gpg](https://gnupg.org/) enryption. However, if you write a script, you should put the check that forces exit in the case of the encryption error.

Google Drive is generous enough to give free 15GB storage for free of charge. There is an open-source utility to interact with Google Drive called [gdrive](https://github.com/prasmussen/gdrive) which I installed using nix. On the first start, it will output a URL from which you can paste it into the browser to authenticate your identity. Then, type this to find what is in your drive

```bash
$ gdrive list
```

Instead of typing file names manually, we can use their API query parameters to get the list of old files. In this case, any file ending with `-Cloud.tar.gz.gpg`. If you want, you can delete every matched file name before uploading new files.

```bash
old=$(gdrive list -q "name contains '-Cloud.tar.gz.gpg'" | awk 'NR > 1 {print $1}')
for i in $old;
  do gdrive delete "$i";
done

gdrive upload "My-Nude-Nobody-Care-Cloud.tar.gz.gpg"
```

## My Final Script

```bash
#!/usr/bin/env bash

function archive_compress_encrypt_upload () {
  dir=$1
  filename=$2

  cd ~ || exit
  echo 'Archiving and Compressing ...'
  tar czf "$filename.tar.gz" --directory="$dir" .

  echo 'Encrypting ...'
  if gpg --encrypt --recipient tkiat@tutanota.com "$filename.tar.gz"
  then
    echo 'Uploading ...'
    gdrive upload "$filename.tar.gz.gpg"
    rm "$filename.tar.gz"
    rm "$filename.tar.gz.gpg"
  else
    echo gpg encryption failed. exiting ...
    exit 1
  fi
}

function delete_old_backup () {
  echo 'Deleting old backups ...'
  old=$(gdrive list -q "name contains '$1'" | awk 'NR > 1 {print $1}')
  for i in $old;
    do gdrive delete "$i";
  done
}

dir=~/Sync/Personal-Cloud

if ! [ -d "$dir" ]; then
  echo "$dir not found. Exiting ..."
  exit 1
fi

echo "If the script hangs, check if authenticated (like typing gdrive list)."

filename_nodate="${dir##*/}"
filename="$(date '+%Y_%m_%d')-$filename_nodate"

delete_old_backup "$filename_nodate.tar.gz.gpg"
archive_compress_encrypt_upload "$dir" "$filename"

echo 'Finish!'
gdrive list
```
