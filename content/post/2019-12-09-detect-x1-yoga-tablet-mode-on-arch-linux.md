---
title: "Detect X1 Yoga Tablet Mode on Arch Linux"
date: 2019-12-09T11:51:47+01:00
tags:
  - Automation
  - X1 Yoga 4th Gen
  - Tablet Mode
  - systemd
---

Inspired from [this ruby script](https://github.com/alesguzik/linux_detect_tablet_mode).

<!--more-->

Add

	%wheel ALL = (ALL) NOPASSWD: /usr/bin/stdbuf -oL -eL libinput debug-events --device /dev/input/by-path/platform-thinkpad_acpi-event

to `/etc/sudoers.d/nopasswd` to allow detecting those event without password.

Run this script as a daemon to enable/disable a `tablet` target..

	#!/usr/bin/env sh

	sudo /usr/bin/stdbuf -oL -eL \
	libinput debug-events --device /dev/input/by-path/platform-thinkpad_acpi-event |
	grep -oP --line-buffered 'switch tablet-mode state \K\d+' |
	while read state; do

	  if [ "$state" -eq 0 ] ; then
	    systemctl --user stop tablet.target
	  elif [ "$state" -eq 1 ] ; then
	    systemctl --user start tablet.target
	  fi

	done

From there you can write `systemd` user services depending on that target. See
for instance this [user service](https://github.com/aureooms/dotfiles/blob/de546107de138493ef6f9d0cb0cefde3962902b8/.config/systemd/user/autorotate.service)
that automatically rotates the screen when in tablet mode.

See also other changes of [that
commit](https://github.com/aureooms/dotfiles/commit/de546107de138493ef6f9d0cb0cefde3962902b8).
