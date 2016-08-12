### UbiFS and logical volumes

To create logical volumes inside ubifs, you need `mtd-utils` and configuration file `ubi.ini`, where volumes are defined. You also need to use `fakeroot` tool, so all generated files have `root` user ownership.

```bash

# install tools needed to create UBIFS image; mkfs.ubifs is included in mtd-utils
sudo apt-get install mtd-utils

# create image from source tree, execute for each volume
fakeroot -- mkfs.ubifs -d </path/to/source/tree/dir> -e 0x1f000 -c 2048 -m 0x800 -x lzo -o <path/to/store/generated/image>
```

In `buildroot` you need to provide `ubi.ini` file, from which rootfs will be generated.

Example of `ubi.ini` file that generates rootfs with three volumes:

```
[www]
mode=ubi
vol_id=0
image=/path/to/ubifs/image
vol_size=64MiB
vol_type=dynamic
vol_name=www
vol_alignment=1

[local]
mode=ubi
vol_id=1
image=/path/to/ubifs/image
vol_size=112MiB
vol_type=dynamic
vol_name=local
vol_alignment=1

[rootfs]
mode=ubi
image=BR2_ROOTFS_UBIFS_PATH
vol_id=2
vol_type=dynamic
vol_name=rootfs
vol_alignment=1
vol_flags=autoresize

```
