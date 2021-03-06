---
title: "Extend Partition and Filesystem on Adjacent Partitions"
date: 2019-04-23T16:01:37+02:00
tags:
  - File System
  - Partitioning
---

To fix your partition out-of-space problems.

<!--more-->

## 1. boot on live USB

## 2. mount

## 3. backup

And also backup partition information of `fdisk -l /dev/sdX`.

## 4. umount

## 5. delete both partitions

To delete `/dev/sdXY` run

	fdisk /dev/sdX
	d
	Y
	p # to check
	w

## 6. create new partition

Use same type and same first block as before

	fdisk /dev/sdX
	n
	e/p
	Y
	p
	w

## 7. check filesystem

Run `e2fsck`

	e2fsck -f /dev/sdXY
	y
	y
	y
	y
	...

## 8. resize

	resize2fs /dev/sdXY

## 9. reboot

The UUID should not have changed so no need to fix `/etc/fstab`.
