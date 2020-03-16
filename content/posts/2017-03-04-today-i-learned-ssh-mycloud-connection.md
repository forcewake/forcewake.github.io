---
categories:
- til
comments: true
date: "2017-03-04T00:00:00Z"
description: How can you fix SSH connection from macOS 10.12 (Sierra) to a My Cloud
tags:
- english
- ssh
- macos
- mac
- Sierra
- mycloud
title: 'Today I Learned: SSH connection from macOS 10.12 to a My Cloud'
---

Use the following command on **_Terminal_** to SSH from a macOS 10.12 (Sierra) computer to a My Cloud: 

1. Double-click on the **Mac HD**, the internal Mac hard drive visible on the top-right corner of the desktop screen.
2. Click on **_Applications_**.
3. Click on **_Utilities_**.
4. Double-click on **_Disk Utility_**.
5. In Disk Utility, double-click on **_Terminal_**.
6. In Terminal, type the following command: 

{{< highlight bash >}}
ssh -oHostKeyAlgorithms=+ssh-dss sshd@wdmycloud.com.local 
{{< / highlight >}}

**Note:**

> If the device name has changed, please use the IP address of the My Cloud instead.

Thanks to [Source](http://support.wdc.com/knowledgebase/answer.aspx?ID=14987 "Permalink to SSH connection from macOS 10.12 (Sierra) to a My Cloud fails")
