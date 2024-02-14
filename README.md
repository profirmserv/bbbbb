# Bespoke BitBake Builder Builder

## Requirements

* A modern systemd-based Linux system (I built this on Ubuntu 23.10.)
* A recent [mkosi][mkosi] (I used v20.2; v14 is too old.)

## Building the builder image

From this directory, run:

```sh
mkosi build
sudo cp -d -t /var/lib/machines mkosi.output/bitbake-debian-11-kirkstone*
```

### Rebuilding

To remove an existing image in `mkosi.output` before building, run `mkosi -f
build`.

A copy of the OS image is cached after package installation but before build
scripts are run. To also remove this cache before building, run `mkosi -ff
build`.

Downloaded distribution packages are also cached. To also remove this cache
before building, run `mkosi -fff build`.

## Using the builder image

Assuming you've installed the image to `/var/lib/machines` and the current
directory is your root of your Bitbake project, run:

```sh
sudo systemd-nspawn \
    --image=/var/lib/machines/bitbake-debian-11-kirkstone --read-only \
    --private-users=pick --private-users-ownership=map \
    --bind-user=$USER --bind=$PWD:/bakery \
    --user=$USER --chdir=/bakery --as-pid2 \
    kas build kas/gfxdemo.yaml
```

### Using a host-wide sstate-cache and downloads

#### Read-write

```sh
sudo systemd-nspawn \
    --image=/var/lib/machines/bitbake-debian-11-kirkstone --read-only \
    --private-users=pick --private-users-ownership=map \
    --bind-user=$USER --bind=$PWD:/bakery \
    --tmpfs=/srv --bind=/srv/bitbake \
    --bind=/run/hashserv-rw \
    --user=$USER --chdir=/bakery --as-pid2 \
    kas build kas/gfxdemo.yaml:kas/shared-state-rw.yaml
```

#### Read-only

```sh
sudo systemd-nspawn \
    --image=/var/lib/machines/bitbake-debian-11-kirkstone --read-only \
    --private-users=pick --private-users-ownership=map \
    --bind-user=$USER --bind=$PWD:/bakery \
    --tmpfs=/srv --bind-ro=/srv/bitbake \
    --bind=/run/hashserv-ro \
    --user=$USER --chdir=/bakery --as-pid2 \
    kas build kas/gfxdemo.yaml:kas/shared-state-ro.yaml
```

## Future work

* Figure out why using `systemd-nspawn --machine=bitbake-debian-11-kirkstone`
  results in the error "No machine for image: bitbake-debian-11-kirkstone".
* Make a mkosi.nspawn file containing the systemd-nspawn options that aren't
  user- or project-specific.
* Make this a bootable image that can be managed by systemd-machined. Add
  instructions for how to use the image via `machinectl` rather than
  `systemd-nspawn`.

[mkosi]: https://github.com/systemd/mkosi
