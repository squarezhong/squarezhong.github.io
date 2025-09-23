---
title: "Config Wake on LAN"
date: 2024-04-10T12:11:03+08:00
# weight: 1
# aliases: ["/first"]
tags: ["System", "Network"]
author: "Square Zhong"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Wake your computer remotely."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/squarezhong.github.io/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

## Introduction

> [Debian Wiki]
> 
> 
> Wake On LAN (WOL) enables other systems on your local area network (LAN) to turn on your system over the network. Support for WOL is required in your network card, motherboard, UEFI/BIOS boot firmware and operating system network configuration.
> 

## Enable WOL

### BIOS

**Normal**: Enable “Wake on LAN” in the BIOS.

**HASEE**: Enable “Network Stack” in the BIOS. It may be set in different place in different bios.

### Windows

By default nothing is needed to do.

If you changed some networking settings, you can use following steps:

[Ethernet Properties] → [Configure] → [Power Management] → check [Allow this device to wake the computer]

### Linux

Check the state of wol:

1. first use `ifcong` to find your ethernet device: e.g. “enp8s0f1” is the name of ethernet device of my laptop
2. check wake-on-lan status: 
`sudo ethtool enp8s0f1 | grep Wake-on`
3. You may see the following content:
    
    ```bash
    Supports Wake-on: pumbg
    Wake-on: d
    ```
    
    if “Wake-on” is “g”, that means you have already turned on wol.
    
4. enable the **wol**: modify the global interface config file /etc/network/interfaces
    
    remember change `enp8s0f1` to your device name
    
    ```bash
    $ sudo vim /etc/network/interfaces
    
    # add following content
    auto enp8s0f1
    iface enp8s0f1 inet dhcp
            ethernet-wol g
    ```
    

WOL modes explanation:

refer to [ethtool manual](https://man7.org/linux/man-pages/man8/ethtool.8.html)

```
       wol p|u|m|b|a|g|s|f|d...
              Sets Wake-on-LAN options.  Not all devices support
              this.  The argument to this option is a string of
              characters specifying which options to enable.
              p   Wake on PHY activity
              u   Wake on unicast messages
              m   Wake on multicast messages
              b   Wake on broadcast messages
              a   Wake on ARP
              g   Wake on MagicPacket™
              s   Enable SecureOn™ password for MagicPacket™
              f   Wake on filter(s)
              d   Disable (wake on nothing).  This option
                  clears all previous options.

```

### Reference

[WakeOnLan - Debian Wiki](https://wiki.debian.org/WakeOnLan#Checking_WOL)

## Use WOL to wake computer

### By command line

Wake other computers.

```python
$ sudo apt install wakeonlan
$ wakeonlan <mac address>
```

In macOS you can use `brew install wakeonlan` to install it.

### Phone App

Many app can use the WOL feature. With some apps and ddns on your router, you can also use WOL feature in a larger local network, such as campus network.