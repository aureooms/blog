---
title: "Backup LUKS header"
date: 2019-10-10T17:15:54+02:00
tags:
  - Security
  - Backup
  - Encryption
  - LUKS
---

LUKS partitions cannot be recovered once the header is lost.

<!--more-->

Create a directory only accessible by root with a `ramfs`

	mkdir /root/<tmp>
	mount ramfs /root/<tmp> -t ramfs

To create a backup image

	cryptsetup luksHeaderBackup /dev/<device> --header-backup-file /root/<tmp>/<file>.img

To encrypt the image with gpg

	gpg2 --recipient <User ID> --encrypt /root/<tmp>/<file>.img

Then move the encrypted file around. Finally, unmount `ramfs` directory

	umount /root/<tmp>

See also https://wiki.archlinux.org/index.php/Dm-crypt/Device_encryption#Backup_and_restore
