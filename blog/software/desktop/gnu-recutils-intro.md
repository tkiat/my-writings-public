# GNU Recutils: A Short Introduction

Some applications, like a personal collection of books, don't need complex databases like MySQL. A lighter alternative like SQLite though it is very simple, it is not human-readable. I would like to introduce GNU Recutils, a lightweight human-readable alternative. This gives you the ability to edit it on the fly without an external application. Like a typical database, it supports command-line commands to add, remove, or update an entry but you require the Recutils package for that.

One project that uses Recutils is GNU Guix System. It stores all packages' definitions in recfile(s) so that a user can make a simple or complex query for packages.

```console
$ guix search emacs
name: emacs
version: 26.3
outputs: out
systems: x86_64-linux i686-linux
dependencies: acl@2.2.53 alsa-lib@1.2.2 dbus@1.12.16 giflib@5.2.1 gnutls@3.6.12 gtk+@3.24.20 imagemagick@6.9.11-11 libice@1.0.10 libjpeg-turbo@2.0.4
+ libotf@0.9.16 libpng@1.6.37 librsvg@2.40.21 libsm@1.2.3 libtiff@4.1.0 libx11@1.6.9 libxft@2.3.3 libxml2@2.9.10 libxpm@3.5.13 m17n-lib@1.8.0
+ mailutils@3.9 ncurses@6.2 pkg-config@0.29.2 texinfo@6.7 zlib@1.2.11
location: gnu/packages/emacs.scm:76:2
homepage: https://www.gnu.org/software/emacs/
license: GPL 3+

...
```

```console
$ guix search openjdk | recsel -p name,version
name: openjdk
version: 9.181
```

As you can see, Recutils just list all the data in a flat hierarchy. It is very suitable for Guix because a field in a package is usually unique and hence very little duplication.

## When Not to Use Recutils?

Recfiles (database files for Recutils) are not suitable to manage files with hierarchies. You will find a lot of duplicate fields when converting hierarchical data structure into a recfiles format. For that, you should prefer something like YAML. See this collection of my 1-dollar Humble Bundle receipt,

```
humble bundle:
  - 2021-06-05 "Web Development & Design by O'Reilly":
      - 2018: 'Designing Web Apis'
      - 2018: 'Learning React'
      - 2018: 'Mastering Modular Javascript'
      - 2018: 'Vue.js Up and Running'
  - 2021-01-06 "Linux by Apress":
      - 2020: 'Hands on Booting'
      - 2018: 'Accelerating Development Velocity Using Docker'
```

On the other hand, the recfile equivalent will require a lot of duplicated fields like 'humble bundle', which is a field shared by all books.
