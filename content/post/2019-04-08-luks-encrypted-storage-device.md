---
title: LUKS encrypted storage device.
date: 2019-04-08T00:00:00+01:00
tags:
  - LUKS
  - Security
  - Encryption
---

So easy to encrypt a USB key, or external or internal drive!

<!--more-->

List disks

	lsblk

Wipe first few bytes to avoid some unfortunate misbehaviours

	dd if=/dev/zero of=/dev/sdx bs=512 count=10

Create a single partition on the storage device

	gdisk /dev/sdx
	o
	y
	n
	<enter>
	<enter>
	<enter>
	<enter>
	w
	y

Create a keyfile

	mkdir -p /root/key
	dd bs=512 count=4 if=/dev/urandom of=/root/key/storage iflag=fullblock

Encrypt main partition

	cryptsetup luksFormat /dev/sdx1 /root/key/storage
	YES

Open main partition and give it a name (storage)

	cryptsetup --key-file /root/key/storage luksOpen /dev/sdx1 storage

Format main partition

	mkfs.ext4 /dev/mapper/storage

Unmount and reboot

	sync
	cryptsetup close storage
