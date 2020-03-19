---
categories:
- music
comments: true
date: "2019-06-20T00:00:00Z"
description: How to play Rocksmith 2014 without the Real Tone Cable and use something like Roland Quad-Capture.
tags:
- english
- music
- rocksmith
- guitar
title: 'How to play Rocksmith 2014 with a Roland Quad-Capture?'
---

TL;DR No hacks or patches required ~~1-minute~~ 1-day way to make it work.

There are a lot of guides about Rocksmith and No Cable Mode both for Windows and Mac but not for Roland Quad-Capture. I don't know actually why but the basic flow is:

> 1. Start Audio MIDI Setup
> 2. Go to Audio Devices
> 3. Click + on the bottom, choose "Create Aggregate Device"
> 4. Rename your new device to "Rocksmith USB Guitar Adapter"

It didn't work for me. So, I tried to find another way. I found an app named [Loopback](https://rogueamoeba.com/loopback/) - usually, people used it to combine mic with audio sources or combine multiple input devices into a single virtual device.
I used it the other way. I've created a virtual device with `Rocksmith USB Guitar Adapter` name where added Roland Quad-Capture Left channel as an output for this virtual device. After that my higher-quality audio interface works as the input instead of the RealTone cable.

![Virtual Rocksmith USB Guitar Adapter](https://imgur.com/QS8vVmF.jpg)

Also, I've changed `Rocksmith.ini` file a little bit:

{{< highlight ini >}}
[Audio]
EnableMicrophone=1
ExclusiveMode=0
LatencyBuffer=4
ForceDefaultPlaybackDevice=1
ForceWDM=0
ForceDirectXSink=0
Win32UltraLowLatencyMode=1
DumpAudioLog=0
MaxOutputBufferSize=0
[Renderer.MacOS]
ScreenWidth=0
ScreenHeight=0
Fullscreen=2
VisualQuality=1
RenderingWidth=0
RenderingHeight=0
EnablePostEffects=1
EnableShadows=1
EnableHighResScope=1
EnableDepthOfField=1
EnablePerPixelLighting=1
MsaaSamples=4
DisableBrowser=0
[Net]
UseProxy=1
[Global]
Version=1
{{< / highlight >}}
