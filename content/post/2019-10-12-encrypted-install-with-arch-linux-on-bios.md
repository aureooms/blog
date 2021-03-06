---
title: Encrypted Install With Arch Linux on BIOS
date: 2019-10-12
tags:
  - Security
  - Encryption
---

So easy!

<!--more-->

Follow the excellent
[video](https://ipfs.xn--mxac.cc/ipfs/QmXK7isJte2LTJhJNzn7PQmNs5aZrcjFRvmXmRr2fhvDVZ).

Check you are NOT in EFI mode

	ls /sys/firmware/efi
	/usr/bin/ls: cannot access '/sys/firmware/efi': No such file or directory

Update the system clock

	timedatectl set-ntp true

List disks

	lsblk

Partition the storage device

	gdisk /dev/sda
	o
	y
	n
	<enter>
	<enter>
	+8M
	ef02
	n
	<enter>
	<enter>
	+2G
	<enter>
	n
	<enter>
	<enter>
	<enter>
	<enter>
	p
	w
	y

Format boot partition

	mkfs.ext4 /dev/sda2

Encrypt main partition

	cryptsetup luksFormat /dev/sda3
	YES
	<passphrase>
	<passphrase>

Open main partition

	cryptsetup luksOpen /dev/sda3 main
	<passphrase>

Partition LVM volume

	pvcreate /dev/mapper/main
	vgcreate main /dev/mapper/main
	lvcreate -L2G main -n swap
	lvcreate -l 100%FREE main -n root


Format root partition

	mkfs.ext4 /dev/mapper/main-root

Format swap partition

	mkswap /dev/mapper/main-swap

Mount partitions

	mount /dev/mapper/main-root /mnt
	mkdir /mnt/boot
	mount /dev/sda2 /mnt/boot
	swapon /dev/mapper/main-swap

Bootstrap installation (tweak `/etc/pacman.d/mirrorlist` if too slow)

	pacstrap /mnt mkinitcpio linux linux-firmware lvm2 base base-devel dhcpcd netctl iw dialog wpa_supplicant wireless_tools vi gvim git firefox
	pacstrap /mnt mkinitcpio linux linux-firmware lvm2 base base-devel dhcpcd vi gvim git firefox # without WiFi

Generate /etc/fstab

	genfstab -p /mnt >> /mnt/etc/fstab

Chroot

	arch-chroot /mnt

Configure time

	rm -f /etc/localtime
	ln -s /usr/share/zoneinfo/Europe/Brussels /etc/localtime
	hwclock --systohc --utc

Create root password

	passwd
	<password>
	<password>

Uncomment `UTF8` and `ISO-8859-1` `en_US` lines in `/etc/locale.gen`.

	# /etc/locale.gen
	en_US.UTF-8 UTF-8
	en_US ISO-8859-1

Then

	locale-gen
	locale > /etc/locale.conf

Configure the hostname

	echo "hostname" > /etc/hostname

Give keyboard access before mounting to type encryption password

	# /etc/mkinitcpio.conf
	HOOKS=(base udev autodetect modconf block keyboard encrypt lvm2 filesystems fsck)

Optionally throw in some `consolefont` and `keymap` hooks if you plan to
customize them.

	# /etc/mkinitcpio.conf
	HOOKS=(base udev autodetect modconf block consolefont keymap keyboard encrypt lvm2 filesystems fsck)

Regenerate initramfs

	mkinitcpio -p linux

Install the bootloader (`grub`)

	pacman -S grub

Edit grub's defaults

	# /etc/default/grub
	...
	GRUB_CMDLINE_LINUX="cryptdevice=UUID=<UUID>:main root=/dev/mapper/main-root rw"
	...

Replace `<UUID>` with the UUID found in `:read !blkid /dev/sda3`.

Install and generate grub's config

	grub-install --target=i386-pc /dev/sda
	grub-mkconfig -o /boot/grub/grub.cfg

Unmount and reboot

	exit
	cd
	sync
	umount -R /mnt
	reboot

Create an admin user

	useradd -m -G wheel theadminusername
	passwd theadminusername
	<password>
	<password>

Uncomment the appropriate `%wheel` line via `visudo`.

	%wheel ALL=(ALL) ALL

Note that the login shell can be changed with

	chsh -s <shell> <username>

The list of available shells is available at `less /etc/shells`.
