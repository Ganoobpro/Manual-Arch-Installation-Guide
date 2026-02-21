# ARCH installation

This file contains all the command that I find to build arch.

## Set up

### Keymap

The first step into linux is load your country key layout.
Here is the command:
```
loadkeys fr # Load France keymap
loadkeys us # Load USA keymap
```

### Make font bigger

```
setfont -d
```

### Connect to WIFI

Using iwctl:

```
systemctl start iwd # (Optional, try when iwctl failed)
iwctl station list # List your WIFI stations
iwctl station wlan0 scan # Scan WIFI around (wlan0 is your default WIFI station)
iwctl station wlan0 connect {WIFI name}
# Type your WIFI password
```

Check connection:

```
ping www.google.com
```



## Partition the disks

```
cfdisk
```

### Delete all partitions, then devide the into 3 parts:

Part 1. 1G         # Boot partition

Part 2. 4G         # Swap partition

Part 3. (The rest) # File system partition

# Finally, WRITE to save changes

Check partitions:

```
lsblk
```

### Format the partitions

```
mkfs.ext4 /dev/sda3
mkfs.fat -F 32 /dev/sda1
mkswap /dev/sda2
```

### Mount the file system

In Linux, mounting a file system is the process of making files and directories on a storage device (like a hard drive, USB stick, or network share) accessible to the user through a specific directory in the computer's single, unified directory tree.

```
mount /dev/sda3 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
swapon /dev/sda2
```



## Install Arch Linux (literally)

A basic installation with the Linux kernel and firmware for common hardware:

```
pacstrap -K /mnt base linux linux-firmware
```

or more complete version:

```
pacstrap -K /mnt base linux linux-firmware sof-firmware base-devel grub efibootmgr networkmanager neovim
```

sof-firmware: low-level audio process

base-devel:   package group that includes tools needed for building (compiling and linking)

grub:         boot loader

efibootmgr:   because using ufi system

## Configure the system

### Generate fstab

fstab (file systems table) is a crucial configuration file located in /etc/fstab on Linux/Unix systems that defines how disk partitions and other data sources are automatically initialized and mounted during boot. It lists partitions, their mount points, file system types, and mount options, ensuring persistent, orderly mounting and preventing load errors. It is primarily managed by system administrators.

```
genfstab /mnt # Just show what genfstab does
genfstab -U /mnt >> /mnt/etc/fstab
```

### Date

```
ln -sf /usr/share/zoneinfo/{Your region} /etc/localtime # Change date
date # Check date

hwclock --systohc # Sync the hardware clock with system clock
```

### Localization

Go to file /etc/locale.gen, remove # from #en_US.UTF-8 UTF-8

Type 'LANG=en_US.UTF-8' into /etc/locale.conf:

```
echo 'LANG=en_US.UTF-8' >> /etc/locale.conf
```

### Hostname, Root, & Users

```
echo 'arch-btw' >> /etc/hostname
```


Password for root:

```
passwd
```

Add users:

```
useradd -m -G wheel -s /bin/bash {user name} # Add user name
passwd {user name} # Add password for user
```

Change default editor to nvim and edit visudo to give user permissions to access sudo:

```
EDITOR=nvim visudo
# Remove # from %wheel ALL=(ALL:ALL) ALL
```

Update pacman as user ...:

```
su {user name}
sudo pacman -Syu
exit
```

### Core Systemd Services

```
systemctl enable NetworkManager # Enable networkmanager
```

### Grub

```
grub-install /dev/sda # Install grub
grub-mkconfig -o /boot/grub/grub.cfg
```



## Done, now REBOOT into Arch btw
