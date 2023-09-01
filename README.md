
# LinuxCNC on raspberry pi 4

* raspbian lite
* RT_PREEMPT patches
* cross-compile raspberry kernel
* setup lightwm + xfce
* compile linuxcnc on first login of "pi" user

## Prerequisites

* aarch64-unknown-linux-gnueabi-gcc cross compiler
* wget
* git
* POSIX system


## Usage
```
$ git clone --recursive https://github.com/heeplr/linuxcncpi
$ cd linuxcncpi
$ ./bootstrap.sh
```
bootstrap.sh will ask for sudo password to mount loopback device & manipulating mounted root partition of image.
After image is generated, you will see a message like:

```
Image creation successful. Copy "..." to an SD card.

(e.g. dd if=.bootstrap-work/raspbian-lite.img of=/dev/sdcard bs=64M status=progress)
```
Replace /dev/sdcard with your sdcard device. Let's assume it's /dev/sdc. Run:

```$ dd if=.bootstrap-work/raspbian-lite.img of=/dev/sdc bs=64M status=progress```

after image is written, safely eject the card. e.g.

```$ eject /dev/sdc``` or ```$ sync```

Boot raspberry pi from card and log in as "pi" user. After bootstrapping finished, reboot.


## Customize

### authorize your SSH pubkey for pi user

Add to ~/.bootstrap.cfg:

```
RPI_BOOTSTRAP_PLUGINS+=( "ssh" )
# authorized ssh pubkeys
RPI_SSH_AUTHORIZE=(
    "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... user1@host"
    "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... user2@host"
)
RPI_SSH_KEYGEN="true"
```

### set wifi config

Add to ~/.bootstrap.cfg:

```
RPI_BOOTSTRAP_PLUGINS+=( "wifi" )
RPI_WIFI_SSID="wifiname"
RPI_WIFI_PSK="wifipassword"
```

### apt install/remove packages

Add to ~/.bootstrap.cfg:

```
RPI_BOOTSTRAP_PLUGINS+=( "apt" )
RPI_APT_CMDS+=(
    "autoremove avahi-daemon bluez cryptsetup-run cryptsetup-initramfs cryptsetup-bin cryptsetup nfs-common ntfs-3g rfkill"
    "install tmux"
)

```

### copy files to image

To copy file "issue" to "/etc/issue" on the image, place "issue" into "~/.bootstrap-dist/etc/issue".
Add to ~/.bootstrap.cfg:

```
RPI_BOOTSTRAP_PLUGINS+=( "dist" )
RPI_DIST_COPY_ON_BAKE+=(
    "/etc/issue"
)
```


