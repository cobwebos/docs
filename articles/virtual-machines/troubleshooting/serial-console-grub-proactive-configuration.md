---
title: Azure 串行控制台主动 GRUB 配置 | Microsoft Docs
description: 跨各种分布配置 GRUB，允许在 Azure 虚拟机中访问单个用户和恢复模式。
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: aba47500400004c1d6a7044a266bad6f20d5d9c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360542"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>主动确保你能够访问 GRUB 和 sysrq，可以节省大量停机时间

在大多数情况下，访问串行控制台和 GRUB 将缩短 IaaS Linux 虚拟机的恢复时间。 GRUB 提供恢复选项，否则恢复 VM 需要更长时间。 


执行 VM 恢复的原因很多，可以归因于以下情况：

   - 损坏的文件系统/内核/MBR（主启动记录）
   - 失败的内核升级
   - 错误的 GRUB 内核参数
   - 错误的 fstab 配置
   - 防火墙配置
   - 密码丢失
   - 损坏的 sshd 配置文件
   - 网络配置

 [此处](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)详细介绍了许多其他情况

验证是否可以访问 Azure 中部署的 VM 上的 GRUB 和串行控制台。 

如果不熟悉串行控制台，请参阅[此链接](./serial-console-linux.md)。

> [!TIP]
> 确保在进行更改之前对文件进行备份

观看下面的视频，了解如何在获得 GRUB 访问权限后快速恢复 Linux VM

[恢复 Linux VM 视频](https://youtu.be/KevOc3d_SG4)

有许多方法可以帮助恢复 Linux VM。 在云环境中，此过程具有挑战性。
在工具和功能方面不断取得进展，以确保服务得到快速恢复。

使用 Azure 串行控制台，你可以像在系统控制台中一样与 Linux VM 进行交互。

可以操作许多配置文件，包括内核的启动方式。 

经验较丰富的 Linux/Unix sys 管理员会喜欢通过 Azure 串行控制台访问的“单一用户”和“紧急模式”，使许多恢复场景的磁盘交换和 VM 删除变得冗余 。

恢复方法取决于遇到的问题，例如，可以通过“Azure 门户选项”->“重置密码”重置丢失或找不到的密码。 “重置密码”功能是一种扩展，该功能与 Linux 来宾代理通信。

可以使用其他扩展（如自定义脚本），但这些选项要求 Linux waagent 处于正常运行状态，而实际情况并不总是这样。

![代理状态](./media/virtual-machines-serial-console/agent-status.png)


确保你有权访问 Azure 串行控制台和 GRUB 意味着密码更改或不正确的配置可以在几分钟（而不是几小时）内得到纠正。 如果磁盘上有多个内核并且主内核损坏，你甚至可以强制 VM 从备用内核启动。

![多内核](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>建议的恢复方法顺序：

- Azure 串行控制台

- 磁盘交换 - 可以使用以下两种方法之一自动执行：

   - [PowerShell 恢复脚本](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Bash 恢复脚本](https://github.com/sribs/azure-support-scripts)

- 旧方法

## <a name="disk-swap-video"></a>磁盘交换视频：

如果你无法访问 GRUB，请观看[此](https://youtu.be/m5t0GZ5oGAc)视频，了解如何轻松地自动执行磁盘交换过程以恢复 VM

## <a name="challenges"></a>挑战：

并非所有 Linux Azure VM 在默认情况下都配置为可访问 GRUB，它们也没有配置为使用 sysrq 命令中断。 某些较旧的发行版（如 SLES 11）未配置为在 Azure 串行控制台中显示登录提示

在本文中，我们将回顾各种 Linux 发行版和文档配置，了解如何使 GRUB 可用。




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>如何配置 Linux VM 以接受 SysRq 密钥
默认情况下，sysrq 密钥在某些较新的 Linux 发行版上启用，尽管在另一些版本上，它可能配置为仅接受特定 SysRq 函数的值。
在较旧的发行版上，它可能被完全禁用。

SysRq 功能可用于直接从 Azure 串行控制台重新启动崩溃或未响应的 VM，也可用于获得对 GRUB 菜单的访问权限，或者从其他门户窗口重新启动 VM 或 ssh 会话可能会放弃当前控制台连接，从而使显示 GRUB 菜单的 GRUB 超时过期。
必须将 VM 配置为接受内核参数值 1，这会启用 sysrq 或 128 的所有函数，从而允许重新启动/关闭电源


[启用 sysrq 视频](https://youtu.be/0doqFRrHz_Mc)


要将 VM 配置为通过 Azure 门户上的 SysRq 命令接受重新启动，需要为内核参数 kernel.sysrq 设置值 1

要使此配置保留重新启动，请向文件 sysctl.conf 添加条目

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

动态配置内核参数

`sysctl -w kernel.sysrq=1`

如果没有根访问权限或 sudo 已损坏，则无法从 shell 提示符配置 sysrq。

在这种情况下，可以使用 Azure 门户启用 sysrq。 如果 sudoers.d/waagent 文件已损坏或已被删除，此方法可能会很有用。

使用“Azure 门户操作”->“运行命令”->“RunShellScript”功能，需要 waagent 过程正常运行，然后你才可以注入此命令以启用 sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

如下所示：![enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

完成后，可以尝试访问 sysrq，应该可以看到可以重新启动。

![enable sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

选择“重新启动”和“发送 SysRq”命令 

![enable sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

系统应记录如下重置消息

![enable sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB 配置

默认情况下，你应该能够在 VM 启动期间按住 Esc 键来访问 GRUB，如果未显示 GRUB 菜单，你可以使用这些选项之一强制将 GRUB 菜单保留在 Azure 串行控制台的屏幕上。

**选项 1** - 强制 GRUB 显示在屏幕上 

更新文件 /etc/default/grub.d/50-cloudimg-settings.cfg，使 GRUB 菜单针对指定的 TIMEOUT 保留在屏幕上。
你无需按 Esc，因为 GRUB 将立即显示

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**选项 2** - 允许在启动前按下 Esc

通过更改文件 /etc/default/grub 可能会发生类似行为，观察 3 秒超时以按 Esc


注释禁止以下两行：

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
然后添加以下行：

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 将允许访问串行控制台，但不提供交互功能。 未显示“登录:”提示


如果 12.04 要获取“登录:”提示：
1. 创建名为 /etc/init/ttyS0.conf 的文件，其中包含以下文本：

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
 
可以在[此处](https://help.ubuntu.com/community/SerialConsoleHowto)找到为 Ubuntu 版本配置串行控制台所需的设置

## <a name="ubuntu-recovery-mode"></a>Ubuntu 恢复模式

可通过 GRUB 为 Ubuntu 提供其他恢复和清理选项，但仅在相应地配置内核参数时才能访问这些设置。
如果未能配置此内核启动参数，系统会强制将恢复菜单发送到 Azure 诊断，而不是发送到 Azure 串行控制台。
可以按照以下步骤访问 Ubuntu 恢复菜单：

中断 BOOT 过程并访问 GRUB 菜单

选择 Ubuntu 的高级选项，然后按 Enter

![屏幕截图显示选定 Ubuntu 的高级选项的串行控制台。](./media/virtual-machines-serial-console/ubunturec1.png)

选择显示“(恢复模式)”的行，请勿按“enter”，而是按“e”

![屏幕截图显示已选择恢复模式版本的串行控制台。](./media/virtual-machines-serial-console/ubunturec2.png)

找到将加载内核的行，并将目标 console=ttyS0 替换为最后一个参数 nomodeset 

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![屏幕截图显示具有更改值的串行控制台。](./media/virtual-machines-serial-console/ubunturec3.png)

按“Ctrl-x”以启动并加载内核。
如果一切顺利，你将看到这些附加选项，这可以帮助执行其他恢复选项

![屏幕截图显示 "恢复" 菜单上的串行控制台，其中提供了其他恢复选项。](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB 配置

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ GRUB 配置
这些版本的默认 /etc/default/grub 配置已充分配置

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
要修改的文件是 /etc/default/grub - 默认配置如下所示：

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

更改 /etc/default/grub 中的以下行

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
 
使用以下命令完成和更新 grub 配置

`grub2-mkconfig -o /boot/grub2/grub.cfg`

设置 SysRq 内核参数：

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

你还可以在 shell 中或通过 Run 命令使用单行配置 GRUB 和 SysRq。 在运行此命令之前备份文件：


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x GRUB 配置
要修改的文件为 /boot/grub/grub.conf。 `timeout` 值将确定 GRUB 的显示时间。

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


通过添加显示 5 秒的“按任意键继续”的提示，最后一行“terminal –-timeout=5 serial console”将进一步增加 GRUB 超时 

![屏幕截图显示带有输出的控制台。](./media/virtual-machines-serial-console/rh6-1.png)

GRUB 菜单应针对配置的 timeout=15 显示在屏幕上，而无需按 Esc。请确保在浏览器的控制台中单击以激活菜单并选择所需的内核

![屏幕截图显示具有两个 Linux 选项的控制台。](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
可以根据官方[文档](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles)使用 YaST 引导加载程序

也将以下参数添加/更改为 /etc/default/grub：

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
验证 ttys0 是否用于 GRUB_CMDLINE_LINUX 或 GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

重新创建 grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
串行控制台出现并显示启动消息，但不显示“登录:”提示

在 VM 中打开 ssh 会话，并通过取消注释此行更新文件 /etc/inittab：

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

接下来运行命令 

`telinit q`

要启用 GRUB，应对 /boot/grub/menu.lst 进行以下更改 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 此配置将使消息“按任意键继续”在控制台上显示 5 秒 

然后，它将再显示 GRUB 菜单 5 秒 - 通过按下向下箭头，你将中断计数器并选择要启动的内核，以为需要设置根密码的单个用户模式追加关键字“single”。

追加命令 init_/bin/bash 将加载内核，但可确保 init 程序替换为 bash shell。

无需输入密码即可访问 shell。 然后，可以继续更新 Linux 帐户的密码或进行其他配置更改。


## <a name="force-the-kernel-to-a-bash-prompt"></a>强制内核到 bash 提示
通过访问 GRUB 可以中断初始化过程，这种交互对于许多恢复过程非常有用。
如果你没有根密码，并且单个用户要求你具有根密码，你可以启动内核，用 bash 提示替换 init 程序 - 此中断可以通过将 init_/bin/bash 追加到内核启动行来实现

![屏幕截图显示具有更新的引导线路的控制台。](./media/virtual-machines-serial-console/bash1.png)

使用以下命令重新装载 / (root) 文件系统 RW

`mount -o remount,rw /`

![屏幕截图显示了具有重新装入操作的控制台。](./media/virtual-machines-serial-console/bash2.png)


现在，你可以执行根密码更改或许多其他 Linux 配置更改

![屏幕截图显示一个控制台，你可以在其中更改根密码和其他配置。](./media/virtual-machines-serial-console/bash3.png)

使用以下命令重启 VM 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>单个用户模式

或者，可能需要在单个用户或紧急模式下访问 VM。 使用箭头键选择希望启动或中断的内核。
通过将关键字“single”或“1”追加到内核启动行，输入所需的模式 。 在 RHEL 系统上，还可以追加 rd.break。

有关如何访问单一用户模式的详细信息，请参阅[此文档](./serial-console-grub-single-user-mode.md#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>后续步骤
详细了解 [Azure 串行控制台]( ./serial-console-linux.md)