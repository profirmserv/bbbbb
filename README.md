# Bespoke BitBake Builder Builder

## Requirements

* A modern systemd-based Linux system (I built this on Ubuntu 23.10.)
* A recent [mkosi][] (I used v20.2; v14 is too old.)

## Building

From this directory, run:

```sh
# Build Debian 11 image for Yocto 4.0
mkosi --image=kirkstone build
sudo cp -d -t /var/lib/machines mkosi.output/bitbake-debian-11-kirkstone*

# Build Debian 12 image for Yocto 4.2
mkosi --image=mickledore build
sudo cp -d -t /var/lib/machines mkosi.output/bitbake-debian-12-mickledore*
```

## Rebuilding

To remove an existing image in `mkosi.output` before building, run `mkosi -f
build`.

A copy of the OS image is cached after package installation but before build
scripts are run. To also remove this cache before building, run `mkosi -ff
build`.

Downloaded distribution packages are also cached. To also remove this cache
before building, run `mkosi -fff build`.

## Future work

* Figure out why using `systemd-nspawn --machine=bitbake-debian-12-mickledore`
  results in the error "No machine for image: bitbake-debian-12-mickledore".
* Make a mkosi.nspawn file containing the systemd-nspawn options that aren't
  user- or project-specific.
* Make this a bootable image that can be managed by systemd-machined. Add
  instructions for how to use the image via `machinectl` rather than
  `systemd-nspawn`.

[mkosi]: https://github.com/systemd/mkosi
