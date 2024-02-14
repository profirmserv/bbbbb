# Bespoke BitBake Builder Builder

## Requirements

* A modern systemd-based Linux system (I built this on Ubuntu 23.10.)
* A recent [mkosi][mkosi] (I used v20.2; v14 is too old.)

## Building the builder image

From this directory, run:

```sh
mkosi build
sudo install -m0644 mkosi.output/bitbake-debian-11-kirkstone* /var/lib/machines
```

To overwrite a built image, run `mkosi -f build`.

A copy of the OS image after package installation but before build scripts are
run is cached. To also clear this cache before building, run `mkosi -ff build`.
To also clear the cache of downloaded .debs, run `mkosi -fff build`.

## Using the builder image

Assuming you've installed the image to `/var/lib/machines` and your current
directory is your root of your Bitbake project, run:

```sh
sudo systemd-nspawn \
    --machine=bitbake-debian-11-kirkstone --read-only \
    --private-users=pick --private-users-ownership=map \
    --bind-user=$USER --bind=$PWD:/bakery \
    --tmpfs=/srv --bind=/srv/bitbake
    --bind=/run/hashserv-rw \
    --user=$USER --chdir=/bakery --as-pid2 \
    kas build kas/gfxdemo.yaml:kas/shared-state-rw.yaml
```

## Future work

* Make a mkosi.nspawn file containing the systemd-nspawn options that aren't
  user- or project-specific.
* Make this a bootable image that can be managed by systemd-machined.
* Add instructions for how to use the image via `machinectl`.

[mkosi]: https://github.com/systemd/mkosi
