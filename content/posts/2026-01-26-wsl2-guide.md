---
title: "WSL: Make Windows Great Again"
date: 2026-01-26
tags:
  - CUDA
  - System
  - Windows
  - linux
  - WSL
author: Square Zhong
description: Windows is the best Linux Desktop
---

<span style="color:blue">[Updated on 2026-09-26]: From CUDA Toolkit to a Comprehensive WSL Tutorial</span>

In the past, Linux has been more advanced than Windows in areas such as command-line interfaces, package managers, build toolchains, containers, automation, and AI ecosystems. With the advent of the Agent era, this gap has widened even further. The right approach is not to stubbornly cling to the Windows ecosystem and seek various alternatives, but rather to embrace Linux (with WSL), combining its strengths in development and automation with Windows' significant advantages in compatibility, graphical user interfaces, and gaming. WSL, make Windows great again.

All mentions of WSL in this article refer to WSL2 by default.
## Install

You can also refer to [Official: How to install Linux on Windows with WSL](https://learn.microsoft.com/en-us/windows/wsl/install)

```powershell
# List available Linux distributions
wsl --list --online
# install
wsl --install <Distro>
# List installed linux distributions
wsl --list --verbose
# set default distribution
wsl --set-default <Distro>
```

- The most recommended WSL distribution is Ubuntu, as the official documentation uses Ubuntu as an example.
- To install a linux distribution using iso file (Not tested)
    [Instructions on how to install a custom distro in WSL2 (Windows SubSystem for Linux 2)](https://gist.github.com/artman41/858450e2c29b239d8692213b684ada25)

## Basic Commands

Systems in WSL2 are standard linux distribution. We can treat it as a normal linux system without desktop.

[Basic commands for WSL](https://learn.microsoft.com/en-us/windows/wsl/basic-commands)

Use `wsl -l -v` to check distribution name.
### Change default user of a distribution

```
<DistributionName> config --default-user <Username>
```

### Run Linux GUI Apps

After installing WSL/WSL2 and updating to a version that supports GUI, you can already run Linux GUI applications.

For example `sudo apt install gimp -y`, then you can find GIMP in your Windows start menu.

### Export and Import

If you want to import your wsl backup in a new machine, you need to **run `wsl --install` first**.

```powershell
wsl --export <Distribution Name> <FileName>
wsl --import <Distribution Name> <InstallLocation> <FileName>
# for example
wsl --import Ubuntu22 D:\wsl\ubuntu22 D:\ubuntu2204.tar
```

### Identify IP Address

```powershell
wsl hostname -i
```
for the IP address of your Linux distribution installed via WSL 2 (the WSL 2 VM address)

```shell
cat /etc/resolv.conf 
```
for the IP address of the Windows machine as seen from WSL 2 (the WSL 2 VM)

### Unregister or uninstall a Linux distribution

```powershell
wsl --unregister <DistributionName>
```

## Configuration

I prefer to use the following config in `%USERPROFILE%\.wslconfig`

```
[wsl2]
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true
```

And enable systemd support in distribution: add following content in `/etc/wsl.conf`

```
[boot]
systemd=true
```

Then execute `wsl --shutdown` in the terminal.

### Networking Mode

Refer to [Official: WSL networking](https://learn.microsoft.com/en-us/windows/wsl/networking) and [Advanced settings configuration in WSL](https://learn.microsoft.com/en-us/windows/wsl/wsl-config)

I believe that Mirrored mode networking is a significant advantage of WSL compared to traditional Linux virtual machines. With mirrored mode, you will have:
- IPv6 support
- Improved networking compatibility for VPNs (very important for users in Mainland China)
- Connect to WSL directly from your local area network (LAN)

### SSH into WSL from another computer

#### In you WSL distribution:

Install ssh server

```shell
sudo apt update
sudo apt install openssh-server
# start ssh
sudo systemctl enable --now ssh
# chech ssh status
sudo systemctl status ssh
```

Change ssh port to 2222 to avoid conflict with Windows OpenSSH server: add `Port 2222` to `/etc/ssh/sshd_config`

```shell
sudo systemctl daemon-reload
sudo systemctl restart ssh.socket
sudo systemctl restart ssh.service
```

#### In your Windows:

Open Hyper-V Firewall: execute the following command as administrator

```powershell
New-NetFirewallHyperVRule `
  -Name "WSL-SSH" `
  -DisplayName "WSL SSH" `
  -Direction Inbound `
  -VMCreatorId '{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}' `
  -Protocol TCP `
  -LocalPorts 2222
```

#### In your other device in the same local network

```shell
ssh <user>@<windows local ip> -p 2222
```

## Development

### Python/C++/Java/Node...

There is no difference in configuration compared to a standard Linux server.

### CUDA Toolkit

1. Install the Windows version of the Nvidia graphics card driver, which will automatically be "mapped" into WSL2. The system will automatically generate files such as `libcuda.so` in the `/usr/lib/wsl/lib/` directory within WSL2.
   <span style="color: red;">Do not install the Linux version of the graphics card driver in WSL2!</span> Doing so may overwrite the mapped Windows version driver, potentially causing issues.

2. Install the required version of the [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0&target_type=deb_network) based on your needs.
   If you only intend to use PyTorch and do not need to compile custom CUDA operators, there is no need to manually install the CUDA Toolkit. Simply follow the official PyTorch documentation for installation, as PyTorch will include the necessary CUDA runtime libraries by default.

## Troubleshooting

### Windows at TUN mode

If your proxy app in Windows is used under TUN mode, **change mtu to 1500**, you will thank me later.

{{< details summary="Why Mihomo's Default MTU 9000 Can Break WSL" open=false >}}
Mihomo uses an MTU of **9000** for its TUN interface mainly as a [performance optimization](https://github.com/MetaCubeX/mihomo/issues/3105). Since the TUN interface is virtual, larger packets reduce packet-processing overhead between the OS kernel and Mihomo.

Normally, this is not a problem because Mihomo processes the traffic before sending it through the physical network, whose MTU is usually **1500**.

However, with **WSL2 Mirrored Networking**, Windows network interfaces are mirrored into WSL. As a result, WSL may see the Mihomo TUN interface with:

```
MTU = 9000
```

and assume that packets up to 9000 bytes can be transmitted directly.

The actual network path is usually closer to:

```
WSL
  ↓
Mihomo TUN      MTU 9000
  ↓
Windows
  ↓
Wi-Fi/Ethernet  MTU 1500
  ↓
Internet
```

WSL may therefore generate packets larger than the real path can carry. If the Windows/WSL networking layer does not correctly return the required **Path MTU Discovery (PMTUD)** feedback, these oversized packets are silently dropped.

This creates an **MTU black hole**:

```
WSL thinks MTU = 9000
        ↓
sends large packets
        ↓
real path only supports ~1500
        ↓
packets are dropped
        ↓
WSL receives no proper MTU feedback
        ↓
connections stall or time out
```

To fix this problem, set Mihomo's TUN MTU to match the normal physical network MTU:

```
tun:
  mtu: 1500
```
{{< /details >}}
