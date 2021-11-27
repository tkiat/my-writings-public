# Linux: Basic Tips

I intend to extend this post over time as I find a new tip.

### Symlink Nonexisting Library

A particular program might not find required libraries. This problem is less common on Windows where the environment is more consistent, but the freedom matters more to me.

The obvious solution is to install that package. On Debian, you can use `apt-file` utility.

```bash
apt-file search <filename>
```

However, unlike a big distro, a smaller distro might only have a few other versions on the system like only libcrypto.so.46.0.1 but not version 47 or 48. You can try making a symlink to that version and hope for backward compatibility.

1.  Find the existing version on your system by typing: find / -name "libcypto.so\*"
2.  If not found, query and install the closest version available (e.g. via the package manager).
3.  Make a symlink and hope for the best! Like

    ```bash
    sudo ln -s /usr/lib/libcrypto.so.46.0.1 /usr/lib/libcrypto.so.1.0.0
    ```

### /var/log is too big

It is generally safe to remove everything except that some apps might assume the folder is there. You might want to skip the folders during the deletion. Keep in mind that this can be harmful in the multi-user environment because someone else might need the log files. You might want to delete periodically except, say, the last 3 days and inform users about it.
