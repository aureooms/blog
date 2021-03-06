---
title: Keyboard Layout Configuration For Encryption Key, Virtual Console And Xorg
date: 2018-06-20T16:31:13+02:00
tags:
  - Keyboard
  - Xorg
  - Virtual Console
---

This post is about everything you ever wanted to ask about keyboard layout
misconfiguration.

<!--more-->

## My keyboard layout is not setup for Xorg

Setup your keymap using `setxkbmap` when launching `X`, for instance in `.xprofile`

    # ~/.xprofile
    setxkbmap be
    setxkbmap -option caps:swapescape

A more robust solution (wireless keyboard plug-n-play ...) is to run the following

    localectl --no-convert set-x11-keymap be '' '' caps:swapescape

It will create a file located at `/etc/X11/xorg.conf.d/00-keyboard.conf`

    # Written by systemd-localed(8), read by systemd-localed and Xorg. It's
    # probably wise not to edit this file manually. Use localectl(1) to
    # instruct systemd-localed to update it.
    Section "InputClass"
            Identifier "system-keyboard"
            MatchIsKeyboard "on"
            Option "XkbLayout" "be"
            Option "XkbOptions" "caps:swapescape"
    EndSection

The option `--no-convert` inhibits the default behaviour of `localectl` which
consists in trying to match the `vconsole` keymap configuration with the `X11` keymap
configuration. This default behaviour should work well with vendor keymaps but
is not needed if you do a manual setup of the virtual console
(see #something)
and is unlikely to work for custom ones
(see #i_want_my_own_keymap).

## My keyboard layout is not setup for the virtual console

Write your chosen keymap in `/etc/vconsole.conf`

    # /etc/vconsole.conf
    KEYMAP=be-latin1

## My keyboard layout is not setup for the encryption key

Put the `keymap` hook before the `keyboard` hook in `/etc/mkinitcpio.conf`

    # /etc/mkinitcpio.conf
    ...
    HOOKS="base udev autodetect modconf block keymap keyboard encrypt filesystems fsck"
    ...

## I want my own keymap

Create your own keymap directory

	mkdir -p /usr/local/share/kbd/keymaps
	cd /usr/local/share/kbd/keymaps

Copy an existing keymap you want to customize

	cp /usr/share/kbd/keymaps/i386/qwerty/us.map.gz personal.map.gz
	gunzip personal.map.gz

Edit `/usr/local/share/kbd/keymaps/personal.map`

Point to it in `/etc/vconsole.conf`

	# /etc/vconsole.conf
	KEYMAP=/usr/local/share/kbd/keymaps/personal.map

See also:

  - [Arch Wiki instructions](https://wiki.archlinux.org/index.php/Keyboard_configuration_in_console#Persistent_configuration)
  - [Some random website](https://wiki.parabola.nu/KEYMAP)
