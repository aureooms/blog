---
title: "Automatically Change Screen Layout when Docked"
date: 2019-12-03T00:50:33+01:00
tags:
  - Automation
  - udev
---

Layout settings can be automated depending on connected monitors.

<!--more-->

Install [`autorandr`](https://github.com/phillipberndt/autorandr) and configure it for the relevant users.

TODO ACTUALLY I DISABLED THAT RULE AND IT STILL WORKS...
I HAVE NO IDEA WHAT TRIGGERS AUTORANDR

Setup a `udev` rule to trigger when docking/undocking.

	# /etc/udev/rules.d/50-dock.rules

	ACTION=="change", BUS="usb", ATTRS{idVendor}=="<idVendor>", ATTRS{idProduct}=="<idProduct>", NAME="dock", RUN="autorandr --batch --change"

The dock id's can be found via `lsusb`.

	lsusb

Reload rules.

	udevadm control --reload-rules

Note that a more flexible solution is to define a service or shell script
that calls other, possibly multiple, scripts.

One can also handle the actions `add` and `remove` with different

See

  - https://unix.stackexchange.com/questions/527437/how-can-i-exectute-a-command-when-my-laptop-is-docked
  - https://unix.stackexchange.com/questions/4489/a-tool-for-automatically-applying-randr-configuration-when-external-display-is-p?noredirect=1
  - https://unix.stackexchange.com/questions/240353/in-udev-what-kind-of-device-events-match-action-change
  - https://stackoverflow.com/questions/13699241/passing-arguments-to-shell-script-from-udev-rules-file
  - https://superuser.com/questions/1033270/how-do-i-use-envsystemd-user-wants-in-udev-rule
  - https://blog.tjll.net/systemd-for-device-activation-and-media-archiving
