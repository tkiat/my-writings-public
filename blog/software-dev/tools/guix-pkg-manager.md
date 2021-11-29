# Guix: A Functional Package Manager

Guix is an awesome functional package manager that is not only available in Guix System but also in foreign Linux distros as well. When the package manager builds some things it chroot to a certain directory whose name is based on the hash value of all predefined inputs in that build process. It puts those input packages and related data to that folder and begins to build it, this build process doesn't affect the current software installed in the system. Guix reuses many of Nix ideas.

## Pros & Cons over Traditional Package Manager

### Pros

- **Build process without side effects**. A build process of a typical Linux distribution has access to all software in the system unlike Guix.
- **Allow the same package of different versions coexist**
- **Enable reproducible build**. Functional package manager enables reproducible build, while a build process of a typical Linux distro might be affected by global system environment variables.
- **Atomic transaction**. In current typical Linux distros, the installation or the removal of a package is prone to interruption which can leave the system in a bad state. Functional package manager enables independent build from the system therefore the installation process is atomic. Because of this, you can also revert to previous states.
- **Libre guaranteed**. It is made by GNU so you can be sure that everything is libre. You can add non-libre packages as well but I don't recommend it since it clashes with the GNU ideology.

### Cons

- **Require more storage space**. Packages from a non-functional package manager, except ones with vendoring, share dependencies across the system so it requires less space.
- **Have fewer packages compared to Nix**.

## Guide

### Installation

Just read the guide on the Guix website.

### Uninstallation from a Foreign Distro

Just like a mall where they provide only an upward escalator, Guix doesn't seem to include the uninstallation procedure in their guide. Assume systemd init,

```bash
sudo systemctl stop guix-daemon.service
sudo systemctl stop gnu-store.mount
sudo rm -rf /gnu
sudo rm -rf /var/guix
sudo rm -rf ~/.profile/guix
sudo rm -rf /etc/guix
```

Make sure you also remove all `guixbuilder` related users and groups.
