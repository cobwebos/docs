---
title: Azure 串行控制台主动 GRUB 配置 |Microsoft Docs
description: 跨各种分发配置 GRUB，允许在 Azure 虚拟机中进行单一用户和恢复模式访问。
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: vilibert
ms.openlocfilehash: a154ab4742f0d0d7acae0376bcf894bc2b62b4cd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186924"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>主动确保你能够访问 GRUB 和 sysrq 可以节省大量停机时间

在大多数情况下，有权访问串行控制台和 GRUB 会缩短 IaaS Linux 虚拟机的恢复时间。 GRUB 提供恢复选项，否则将需要更长的时间来恢复 VM。 


执行 VM 恢复的原因有很多，可将其视为方案：

   - 损坏的文件系统/内核/MBR （主启动记录）
   - 内核升级失败
   - GRUB 内核参数不正确
   - Fstab 配置不正确
   - 防火墙配置
   - 密码丢失
   - 损坏的 sshd 配置文件
   - 网络配置

 [此处](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)详细介绍的许多其他方案

验证是否可以访问在 Azure 中部署的 Vm 上的 GRUB 和串行控制台。 

如果不熟悉串行控制台，请参阅[此链接](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/)。

> [!TIP]
> 确保在进行更改之前对文件进行备份

观看以下视频，了解你可以在访问 GRUB 后快速恢复 Linux VM

[恢复 Linux VM 视频](https://youtu.be/KevOc3d_SG4)

有多种方法可帮助恢复 Linux Vm。 在云环境中，此过程非常困难。
正在不断地对工具和功能进行进度，以确保快速恢复服务。

使用 Azure 串行控制台，可以与 Linux VM 交互，就像在系统控制台上一样。

你可以操作多个配置文件，包括内核的启动方式。 

经验丰富的 Linux/Unix sys 管理员将会对可通过 Azure 串行控制台访问的**单用户**和**紧急模式**感到满意，为多个恢复方案提供磁盘交换和 VM 删除。

恢复方法取决于遇到的问题，例如丢失或错放的密码可以通过 Azure 门户选项重置->**重置密码**。 **重置密码**功能称为扩展，并与 Linux 来宾代理通信。

可以使用其他扩展（如自定义脚本），但这些选项要求 Linux **waagent**处于正常状态，但并非总是如此。

![代理状态](./media/virtual-machines-serial-console/agent-status.png)


确保你有权访问 Azure 串行控制台和 GRUB 意味着在几分钟而不是几小时内可以纠正密码更改或配置不正确。 甚至可以强制 VM 从备用内核启动，因为在主内核损坏的情况下，在磁盘上有多个内核。

![多内核](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>建议的恢复方法顺序：

- Azure 串行控制台

- 磁盘交换-可以使用以下两种方法之一自动实现：

   - [Power Shell 恢复脚本](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash 恢复脚本](https://github.com/sribs/azure-support-scripts)

- 旧方法

## <a name="disk-swap-video"></a>磁盘交换视频：

如果你无权访问 GRUB 观看[此](https://youtu.be/m5t0GZ5oGAc)视频，请参阅如何轻松地自动执行磁盘交换过程以恢复 VM

## <a name="challenges"></a>带来

默认情况下，并非所有 Linux Azure Vm 都配置为进行 GRUB 访问，它们均未配置为使用 sysrq 命令中断。 一些较旧的发行版（如 SLES 11）未配置为在 Azure 串行控制台中显示登录提示

在本文中，我们将回顾各种 Linux 分发和文档配置，了解如何使 GRUB 可用。




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>如何将 Linux VM 配置为接受 SysRq 密钥
默认情况下，在一些较新的 Linux 发行版上启用了 sysrq 项，但在其他情况下，它可能配置为仅接受某些 SysRq 函数的值。
在较旧的发行版上，可能会完全禁用它。

SysRq 功能可用于直接从 Azure 串行控制台重启崩溃或挂起的 VM，同时还有助于获取对 GRUB 菜单的访问权限，或者从另一个门户窗口或 ssh 会话重启 VM 可能会删除当前的控制台连接从而使用于显示 GRUB 菜单的 GRUB 超时过期。
必须将 VM 配置为接受内核参数的值1，这将启用 sysrq 或128的所有函数，这允许重启/关机


[启用 sysrq 视频](https://youtu.be/0doqFRrHz_Mc)


若要将 VM 配置为在 Azure 门户上通过 SysRq 命令接受重新启动，需要将内核参数 SysRq 的值设置为1。

要使此配置持续重新启动，请将条目添加到**sysctl**文件

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

动态配置内核参数

`sysctl -w kernel.sysrq=1`

如果没有**根**访问权限或 sudo 断开，则无法从 shell 提示符下配置 sysrq。

在此方案中，可以使用 Azure 门户来启用 sysrq。 如果**sudoers/waagent**文件已损坏或已被删除，此方法可能很有用。

使用 Azure 门户操作-> 运行命令-> RunShellScript 功能，要求 waagent 进程正常运行，然后可以注入此命令以启用 sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

如下所示： ![enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

完成后，可以尝试访问**sysrq** ，并会看到可能重新启动。

![启用 sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

选择**Reboot**并**发送 SysRq**命令

![启用 sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

系统应记录重置消息，如此

![启用 sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB 配置

默认情况下，你应该能够通过在 VM 启动期间按下**Esc**键来访问 grub，如果不显示 grub 菜单，你可以使用以下选项之一在 Azure 串行控制台中强制并保留 grub 菜单。

**选项 1** -强制在屏幕上显示 GRUB 

更新文件/etc/default/grub.d/50-cloudimg-settings.cfg 以使 GRUB 菜单在屏幕上保持指定的超时时间。
不需要按**Esc**才能立即显示 GRUB

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**选项 2** -允许在启动前按**Esc**

通过对文件/etc/default/grub 进行更改来体验类似行为，并观察到按**Esc**的3秒超时


注释掉以下两行：

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
并添加以下行：

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 将允许访问串行控制台，但不提供交互功能。 **登录名：** 未显示提示


对于12.04 获取**登录名：** prompt：
1. 创建一个名为/etc/init/ttyS0.conf 的文件，其中包含以下文本：

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. 要求 upstart 启动 getty     
    ```
    sudo start ttyS0
    ```
 
可在[此处](https://help.ubuntu.com/community/SerialConsoleHowto)找到为 Ubuntu 版本配置串行控制台所需的设置

## <a name="ubuntu-recovery-mode"></a>Ubuntu 恢复模式

其他恢复和清理选项可通过 GRUB 用于 Ubuntu，但是，仅当你配置了内核参数时，才可以访问这些设置。
如果无法配置此内核启动参数，将强制将恢复菜单发送到 Azure 诊断，而不是发送到 Azure 串行控制台。
可以通过以下步骤获取对 Ubuntu 恢复菜单的访问权限：

中断启动进程和访问 GRUB 菜单

选择 Ubuntu 的高级选项，然后按 enter

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

选择行显示 *（恢复模式）* 不要按 enter，而是按 "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

找到将加载内核的行，并将**nomodeset**中的最后一个参数替换为**console = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

按**Ctrl + x**启动并加载内核。
如果一切顺利，你会看到这些附加选项，可帮助执行其他恢复选项

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB 配置

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ GRUB 配置
已充分配置这些版本上的默认/etc/default/grub 配置

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

启用 SysRq 键

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat 7\.2 和 7\.3 GRUB 配置
要修改的文件是/etc/default/grub –默认配置如下例所示：

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

更改/etc/default/grub 中的以下行

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

还要添加以下行：

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub 现在应类似于以下示例：

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
使用完成并更新 grub 配置

`grub2-mkconfig -o /boot/grub2/grub.cfg`

设置 SysRq 内核参数：

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

你还可以在 shell 中或通过运行命令使用一行来配置 GRUB 和 SysRq。 运行此命令之前，请备份文件：


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x GRUB 配置
要修改的文件为/boot/grub/grub.conf。 `timeout` 值将决定显示 GRUB 的时间长度。

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


最后一行*终端–超时 = 5 串行控制台*将通过添加5秒的提示 **，按任意键继续，** 增加**GRUB**超时。

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

GRUB 菜单应在屏幕上显示配置的超时 = 15，无需按 Esc。请确保在浏览器的控制台中单击，使菜单激活，并选择所需的内核

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
按官方[文档](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)使用 yast 引导加载

或者将以下参数添加/更改为/etc/default/grub：

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
验证 ttys0 是否在 GRUB_CMDLINE_LINUX 或 GRUB_CMDLINE_LINUX_DEFAULT 使用

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

重新创建 grub。

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
串行控制台随即出现并显示启动消息，但不显示**登录名：** prompt

在 VM 中打开 ssh 会话，并通过取消注释此行来更新文件 **/etc/inittab** ：

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

接下来运行命令 

`telinit q`

若要启用 GRUB，应对/boot/grub/menu.lst 进行以下更改 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 此配置将启用消息 "**按任意键以继续**在控制台上显示5秒 

然后，它会将 GRUB 菜单显示额外的5秒-通过按向下箭头将中断计数器，并选择要启动的内核，并为需要设置 root 密码的单用户模式追加关键字**single** 。

追加命令**init =/bin/bash**将加载内核，但会确保 init 程序替换为 bash shell。

你将获得对 shell 的访问权限，而无需输入密码。 然后，你可以继续更新 Linux 帐户的密码或进行其他配置更改。


## <a name="force-the-kernel-to-a-bash-prompt"></a>强制内核使用 bash 提示符
如果能够访问 GRUB，则可以中断初始化过程。此交互适用于许多恢复过程。
如果你没有 root 密码，并且单一用户要求你具有根密码，则可以使用 bash 提示符启动内核替换 init 程序–可以通过将 init =/bin/bash 追加到内核引导行来实现此中断

![bash1](./media/virtual-machines-serial-console/bash1.png)

使用命令重新装载/（根）文件系统 RW

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


现在，你可以执行根密码更改或其他很多 Linux 配置更改

![bash3](./media/virtual-machines-serial-console/bash3.png)

重新启动 VM 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>单用户模式

或者，可能需要在单用户或紧急模式下访问 VM。 使用箭头键选择要启动或中断的内核。
通过向内核引导行追加关键字**single**或**1**来输入所需模式。 在 RHEL 系统上，还可以追加 " **rd**"。

有关如何访问单用户模式的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>后续步骤
详细了解[Azure 串行控制台]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)