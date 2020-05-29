---
categories:
- code
comments: true
date: "2020-05-29T00:10:00Z"
description: How to Install MacOS High Sierra as a VM in Unraid.
tags:
- english
- unraid
- macos
- vm
title: 'How to Install MacOS High Sierra as a VM in Unraid with pass through NVIDIA GPU'
---

TL;DR Using SpaceinvaderOne's docker app named `Macinabox` install MacOS, then install Splashtop Streamer and NVIDIA WebDriver for GPU and then update XML config

# Macinbox

Start from this [SpaceinvaderOne's video](https://youtu.be/g_jk9D2e5q0) about Macinbox - you will get all information

Then install Macinbox - it will download all needed stuff for you and install MacOS

# Apps

After that login to VM and install two apps:
1. `webdriver.sh` - Bash script for managing NVIDIA's web drivers on macOS High Sierra and later with an option to set the required build number in NVDAStartupWeb.kext and NVDAEGPUSupport.kext.
2. Splashtop Personal - Remote Desktop gives you high-performance remote access to your PC or Mac from your Windows tablet. Splashtop is the ONLY remote access product capable of delivering full audio and HD video streaming and even interactive 3D gaming. (Access across the Internet requires purchase of the optional Anywhere Access Pack.)

## webdriver.sh

Install `webdriver.sh` with Homebrew

{{< highlight bash >}}
brew tap vulgo/repo
brew install webdriver.sh
{{< / highlight >}}

Update to the latest release
{{< highlight bash >}}
brew upgrade webdriver.sh
{{< / highlight >}}

Install the latest drivers

{{< highlight bash >}}
webdriver
{{< / highlight >}}

Installs/updates to the latest available NVIDIA web drivers for your current version of macOS.

## Splashtop Personal
Just download the app from website and install it (it's free for personal usage in local environment)

# XML configuration

You should update the xml configuration each time after you changed something using 'UI' interface (memory, GPU or CPU core count)

## Ethernet

Update original ethernet config section with 
{{< highlight xml >}}
<interface type='bridge'>
  <mac address='12:14:10:17:2f:f5'/>
  <source bridge='br0'/> 
  <model type='vmxnet3'/>
  <address type='pci' domain='0x0000' bus='0x01' slot='0x00' function='0x0'/>
</interface>
{{< / highlight >}}

or

{{< highlight xml >}}
<interface type='bridge'>
  <mac address='12:14:10:17:2f:f5'/>
  <source bridge='br0'/> 
  <model type='e1000-82545em'/>
  <address type='pci' domain='0x0000' bus='0x01' slot='0x00' function='0x0'/>
</interface>
{{< / highlight >}}

## Command line arguments

Put in the very botton of the XML config

{{< highlight xml >}}
<qemu:commandline>
  <qemu:arg value='-usb'/>
  <qemu:arg value='-device'/>
  <qemu:arg value='usb-kbd,bus=usb-bus.0'/>
  <qemu:arg value='-device'/>
  <qemu:arg value='isa-applesmc,osk=ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc'/>
  <qemu:arg value='-smbios'/>
  <qemu:arg value='type=2'/>
  <qemu:arg value='-cpu'/>
  <qemu:arg value='Penryn,kvm=on,vendor=GenuineIntel,+invtsc,vmware-cpuid-freq=on,+pcid,+ssse3,+sse4.2,+popcnt,+avx,+aes,+xsave,+xsaveopt,check'/>
</qemu:commandline>
{{< / highlight >}}

# Tips

## OS line
You may get duplicate apple image on boot up after, if you so you should change the OS line:

{{< highlight xml >}}
<os>
  <type arch='x86_64' machine='pc-q35-3.1'>hvm</type>
  <loader readonly='yes' type='pflash'>/mnt/user/domains/MacinaboxHighSierra/ovmf/OVMF_CODE.fd</loader>
  <nvram>/mnt/user/domains/MacinaboxHighSierra/ovmf/OVMF_VARS.fd</nvram>
</os>
{{< / highlight >}}

## Icon

Just in case you want to have nice icon in VM manager

{{< highlight xml >}}
<vmtemplate xmlns="unraid" name="MacOS" icon="/mnt/user/domains/MacinaboxHighSierra/icon/highsierra.png" os="HighSierra"/>
{{< / highlight >}}
