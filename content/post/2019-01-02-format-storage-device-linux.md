---
title: Format Storage Device Linux
date: 2019-01-02T23:58:47+01:00
tags:
  - Format
  - Storage
---

Very handy!

<!--more-->

(As root)

First wipe first few bytes to avoid some unfortunate misbehaviours

	dd if=/dev/zero of=/dev/sdx bs=512 count=1

Partition the disk

	fdisk /dev/sdx
	o
	n
	<enter>
	<enter>
	<enter>
	w

Then use the format you fancy

	mkfs.ext4 /dev/sdx1
