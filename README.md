# Bespoke BitBake Builder Builder

## Requirements

* A modern systemd-based Linux system (I built this on Ubuntu 23.10.)
* A recent [mkosi][mkosi] (I used v20.2; v14 is too old.)

## Building the builder

From this directory, run:

```sh
mkdir -p mkosi.builddir # persistence for mkosi.build
mkdir -p mkosi.cache # persistence for distro packages
sudo mkosi -i -f build
```

The "image" (directory tree, really) will end up in `/var/lib/machines`.

[mkosi]: https://github.com/systemd/mkosi
