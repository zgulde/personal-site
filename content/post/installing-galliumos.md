+++
date = "2016-09-04T21:24:28-05:00"
draft = false
title = "Installing Gallium OS On A Chromebook"
+++

# Installing Galliumos On An Acer Chromebook

A guide to installing [Gallium OS](https://galliumos.org/) on a chromebook. This
guide is mostly a rehash of the information found on [the
wiki](https://wiki.galliumos.org/Welcome_to_the_GalliumOS_Wiki), but in my own
words and consolidated in one place. [Skip past the background section if you're just
here for a walkthrough.](#steps)

This was all done with an [ACER CB3-131](https://www.amazon.com/Acer-Chromebook-CB3-131-C3SZ-11-6-Inch-Dual-Core/dp/B019G7VPTC).

## Background

I've been running linux for fun and a little bit of programming work on my acer
chromebook for a while now. I've primarily been using
[crouton](https://github.com/dnschneid/crouton) with Ubuntu, and was able to
have a pretty nice environment with all of my favorite tools (vim, python, ruby,
etc...), but I was never totally happy with my setup. 

Being able to switch between chrome os and my xfce desktop with a keyboard
shortcut was pretty cool, but the desktop always felt a little slow, especially
with multiple windows open, and I spent too much time trying to get the trackpad
sensitivity setup right. For the longest time I forewent the desktop, and ran a
cli only ubuntu install. I'd work with a terminal tab running ubuntu open in
chromeos and multiple other tabs for web browsing. This worked pretty nice, but
also had it's issues (namely sending ctrl and alt keyboard combinations properly
to the terminal). Also, a problem with both of of these setups was installing
any sory of serious dev environment, which is fine for the small little hobby
projects I'm working on, but I also wanted to have the ability to work on more
serious projects. I don't have enough knowledge to say that it's impossible to
get virtualbox or docker running properly on chromeos with crouton, but it
certainly has me beat.

I finally decided to just pull the trigger and put a full linux os on my
chromebook. Enter [Gallium OS](https://galliumos.org/). Gallium OS is a linux
distribution based off of Ubuntu that's targeted specifically at chromebooks.
Gallium promises take care of all the tedious linux configuration(sleeping
properly, touchpad issues, battery life, etc...) out of the box for chromebooks.
So far, for me, it's lived up to the promise.

As anyone who has ever installed Linux knows, all the resources that help you
get a distribution up and running on any specific machine are never in one
place, so this post is my effort to help consolidate some of those resources and
provide a walkthrough on the gallium os install. My goal is to put everything
you need for the install on this page, and keep from saying you need to go to an
external page to a minimum.

## Steps

*Note that this process* **_will_** *erase any data you have on your chromebook,
so make sure you back up any data you don't want to lose before following
along.*

1. [download the appropriate `.iso` and put it on a usb stick](#download-the-iso)
1. [put your chromebook into developer mode](#enabling-developer-mode-on-your-chromebook)
1. [update the firmware on your chromebook](#updating-the-chromebooks-firmware)
1. [boot from the usb stick and install gallium](#install-gallium)
1. (optional) spend hours tinkering with different color schemes and window
   styles

Note that the process of downloading the `.iso` and creating the usb stick are
usually the longest steps in this process, so they can be done at the same time
you are prepping your chromebook for the install.

## Download the `.iso`

Firstly we'll need to check if gallium supports the chromebook you're trying to
target. Have a look at the [hardware compatibility
list](https://wiki.galliumos.org/Hardware_Compatibility) and make note of the
the processor model you have and whether or not you'll need to update the
firmware (most will probably need to do this). 

Go ahead and [download the appropriate `.iso`](https://galliumos.org/download)
for your processor model, found on the preceding hardware compatibiity list.

## Making a bootable usb stick

The instructions for this will vary depending on where and on what machine you
downloaded the `.iso` on. I've successfully done this process on OSX, but I
cannot speak to the processon Windows and Linux. Luckily the Gallium docs have a
lot of good information on this.

[Here's the reference page from Gallium OS's docs on creating a bootable usb
drive](https://wiki.galliumos.org/Installing/Creating_Bootable_USB)

### OSX

Without your usb drive plugged in run

```
diskutil list
```

and make note of the devices listed.

Go ahead and plug the drive in and run `diskutil list` again, and identify the
new device listed.  This is your usb drive. Note the path to the device, it
should be something like 

```
/dev/disk3
```

*Note that if you misidentify your usb drive you could potentially cause data
corruption and / or loss. Make sure that you are sure which device is your usb
drive.*

For the following section, replace `disk3` with the device you identified.

Go ahead and unmount the device.

```
diskutil unmountDisk /dev/disk3
```

Now we will put the `.iso` on the drive

```
# replace the filepath and name with wherever you downloaded the .iso file
sudo cp ~/Downloads/galliumos.iso /dev/rdisk3
```

Note the `r` before the `disk3` in the above command.

You won't see any output from this command, and it might take a while to run,
but don't worry, `cp` is quietly doing it's thing.

When that command finishes OSX might popup and tell you it doesn't recognize the
usb drive. That's good, it means everything went as intended. If you see a popup
box telling you this, go ahead and click 'unmount'. If you don't see a popup
box, run 

```
diskutil unmount /dev/disk3
```

And now your usb drive will be good to go.

## Enabling Developer Mode On Your Chromebook

With your chromebook off, hold Esc + F3 and then hold the power button. Your
chromebook should boot into a screen that tells your that chromeos is damaged or
missing (Don't worry, its not). From this screen press `ctrl + d` to enable
developer mode.

When you reboot, you'll see the so-called 'scare screen' telling you that os
verification is turned off. That's okay! we know what we're doing. Go ahead and
press `ctrl + d` again to go ahead and boot the os.

## Getting a shell

There are a couple different ways to get a bash shell on your developer mode
chromebook.

### Through the ChromeOS interface

Now that we've enabled developer mode we can get into a bash shell by pressing
`ctrl + alt + t` to get to the chronos shell (chrosh) and typing `shell`.

```
crosh> shell
```

### Without logging in

If you don't want to go through the trouble of signing in with your google
account, you don't have to!

From the login screen, press `ctrl + alt + f2` where on most, if not all,
chromebooks f2 is the right arrow key at the very top of the keyboard.

This will take you to a login screen. If you have developer mode enabled, you'll
be able to enter the login name `chronos`, and no password.

## Updating the chromebook's firmware

This is not nearly as scary as it sounds. There are several different firmwares
out there, but I went with [MrChromebox](https://mrchromebox.tech/) because
there's a simple shell script that does all the heavy lifting for you and it's
first on the list.

To update our firmwate, we'll download the utility shell script and run it to
enable legacy boot mode

Get into a shell and run

```
sudo crossystem dev_boot_legacy=1
```

and then

```
curl -LO http://mrchromebox.tech/firmware-util.sh
sudo bash firmware-util.sh
```

Tell the script you want to enable legacy boot mode. 

After this finishes, go ahead and power down your chromebook to prepare for the
next step.

## Install Gallium

With the power off, insert the usb stick into your chromebook and boot it up.

*Note that some chromebooks will only boot from usb 2.0 ports.* Usually usb 3.0
ports are blue, while 2.0 is black. To be on the safe side, make sure the usb
drive is in a black usb port.

You'll see the white scare screen, go ahead and hit `Ctrl + L` and select the
option to boot from the usb stick(this should be selected by default). The live
gallium os will boot up, feel free to poke around in here, but when you're done,
click on the only icon on the desktop to begin the installation process. Click
through and fill in the neccessary information in the setup wizard, and you
should start installing Gallium OS.

## Boot it up!

After the installation process completes and your machine reboots, you'll see
the white scare screen again. Don't worry, just hit `ctrl + l` to boot into
gallium. You'll see a blank screen for a second, and then gallium's loading
screen! From here you can log in with the username and password you setup in the
install process and start exploring your new os. Change the colorschemes, [copy
over your config files](https://github.com/zgulde/dotfiles), or [make your
capslock (or search) key do something useful](/post/make-capslock-useful/).

Note that you will see the chrome scare screen every time you boot, but your new
os is just a quick `ctrl + l` away!

## Sources and Handy Links

- [Gallium OS Hardware Compatibility Table](https://wiki.galliumos.org/Hardware_Compatibility)
- [Gallium OS Download Page](https://galliumos.org/download.html)
- [Gallium OS Wiki Page on Installation](https://wiki.galliumos.org/Installing/Preparing)
- [Gallium OS Wiki on Firmware](https://wiki.galliumos.org/Firmware)
- [Creating a Bootable USB drive](https://wiki.galliumos.org/Installing/Creating_Bootable_USB)
- [MrChromebox Page](https://mrchromebox.tech/)
