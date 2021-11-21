---
categories:
- code
comments: true
date: "2021-11-21T00:10:00Z"
description: How to run Docker inside a virtual machine with Windows 10 in Unraid.
tags:
- english
- unraid
- nested virtualization
- vm
- docker
title: 'How to run Docker inside a virtual machine with Windows 10 in Unraid'
---

TL;DR Shutdown all VMs than run `modprobe -r kvm_intel` and `modprobe kvm_intel nested=1` in unraid terminal. After that add `<feature policy='require' name='vmx'/>`
to the CPU Section of target VM.

# VM

Make sure that all VM are stopped. Wait a little bit after stopping.

# Open the Unraid Terminal

Run the following via a terminal in unraid (SpaceInvader's thank you!)

{{< highlight bash >}}
#!/bin/bash

modprobe -r kvm_amd
modprobe kvm_amd nested=1
echo "Nested vms are enabled for AMD cpus"

sleep 10
exit
{{< / highlight >}}

# Open VM Settings -> Toggle XML View

Add feature to to the CPU Section

{{< highlight xml >}}
<feature policy='require' name='vmx'/>
{{< / highlight >}}

# Tips

## Any way to reverse these commands?

{{< highlight bash >}}
modprobe -r kvm_amd
modprobe kvm_amd nested=0
{{< / highlight >}}
