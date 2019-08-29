---
title: GRUB 和单用户模式的 Azure 串行控制台 | Microsoft Docs
description: 在 Azure 虚拟机中使用 grub 串行控制台。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 1bd850fe2cac7194d78005f4c0a57523bc8323c6
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124487"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>使用串行控制台访问 GRUB 和单用户模式
GRUB 是指 GRand 统一引导加载程序，它可能是你在启动 VM 时看到的第一个事物。 因为它是在操作系统启动之前显示的，因此无法通过 SSH 进行访问。 从 GRUB 可以修改启动配置以实现启动进入单用户模式等功能。

单用户模式是包含最少量功能的极简环境。 它可用于调查启动问题、文件系统问题或网络问题。 可在后台运行的服务较少，根据具体的运行级别，甚至可以不自动装载文件系统。

在仅将 VM 配置为接受 SSH 密钥登录的情况下，单用户模式也很有用。 在这种情况下，可以使用单用户模式创建具有密码身份验证的帐户。 请注意，串行控制台服务将仅允许具有参与者级别或更高级别访问权限的用户访问 VM 的串行控制台。

若要进入单用户模式，需要在 VM 启动时输入 GRUB，并在 GRUB 中修改启动配置。 下面介绍了进入 GRUB 的详细说明。 通常，如果你的 VM 已配置为显示 GRUB，则可以使用 VM 串行控制台中的重启按钮来重启 VM 并显示 GRUB。

![Linux 串行控制台重启按钮](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>通用 GRUB 访问
若要访问 GRUB，需要重新启动 VM，同时使串行控制台边栏选项卡保持处于打开状态。 某些发行版将需要键盘输入以显示 GRUB，而其他发行版则会自动显示 GRUB 几秒钟，并允许用户键盘输入取消超时。

需要确保 VM 上已启用 GRUB，以便能够访问单用户模式。 根据所用的分发版，可能需要完成一些设置工作才能确保启用 GRUB。 可在下方的[此链接](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)处查看特定于发行版的信息。

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>重启 VM 以在串行控制台中访问 GRUB
可以通过在串行控制台中导航到电源按钮并单击“重启 VM”来重启 VM。 这将发起 VM 重启，你将在 Azure 门户中看到有关重启的通知。
如果启用了 [SysRq](./serial-console-nmi-sysrq.md)，则还可以使用 SysRq `'b'` 命令来重启 VM。 按照下面的特定于发行版的说明，了解在重新启动时应该从 GRUB 中得到什么。

![Linux 串行控制台重启](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>常规单用户模式访问
在未配置具有密码身份验证的帐户的情况下，可能需要手动访问单用户模式。 需要修改 GRUB 配置以手动进入单用户模式。 完成此操作后，请参阅“使用单用户模式重置或添加密码”以获取进一步说明。

如果 VM 无法启动，某些发行版通常会自动将你置于单用户模式或紧急模式。 但是，其他发行版需要进行额外设置才能自动将你置于单用户模式或紧急模式（例如设置根密码）。

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>使用单用户模式重置或添加密码
进入单用户模式后，请执行以下操作以添加具有 sudo 权限的新用户：
1. 运行 `useradd <username>` 来添加用户
1. 运行 `sudo usermod -a -G sudo <username>` 向新用户授予根权限
1. 使用 `passwd <username>` 为新用户设置密码。 然后，你将能够以新用户身份登录


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>在 Red Hat Enterprise Linux (RHEL) 中访问
在无法正常启动的情况下，RHEL 会自动将你置于单用户模式。 但是，如果未设置单用户模式的 root 访问权限，则就不会获得 root 密码，因此也就无法登录。 有一种解决方法（请参阅后面的“手动进入单用户模式”），但建议的做法是一开始就设置 root 访问权限。

### <a name="grub-access-in-rhel"></a>在 RHEL 中访问 GRUB 访问
RHEL 原本就启用了 GRUB。 若要进入 GRUB，请使用 `sudo reboot` 重新启动 VM，然后按任意键。 此时会显示 GRUB 屏幕。 如果未显示, 请确保 GRUB 文件 (`/etc/default/grub`) 中存在以下行:

#### <a name="rhel-8"></a>RHEL 8:
```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

#### <a name="rhel-7"></a>RHEL 7:
```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat 也提供了有关启动进入急救模式、紧急模式、调试模式以及重置 root 密码的文档。 [单击此处访问文档](https://aka.ms/rhel7grubterminal)。

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>在 RHEL 中为单用户模式设置 root 访问权限
RHEL 中的单用户模式要求启用 root 用户（默认已禁用）。 如果需要启用单用户模式，请遵照以下说明：

1. 通过 SSH 登录到 RedHat 系统
1. 切换到 root
1. 启用 root 用户的密码
    * `passwd root`（设置强 root 密码）
1. 确保 root 用户只能通过 ttyS0 登录
    * 运行 `edit /etc/ssh/sshd_config` 并确保 PermitRootLogIn 设置为 no
    * 运行 `edit /etc/securetty file`，以便只允许通过 ttyS0 登录

现在，如果系统启动到单用户模式，则你可以通过 root 密码登录。

对于 RHEL 7.4+ 或 6.9+ 版本，可以在 GRUB 提示符下启用单用户模式，请参阅的[此处](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)的说明

### <a name="manually-enter-single-user-mode-in-rhel"></a>在 RHEL 中手动进入单用户模式
如果已遵照上面的说明设置了 GRUB 和 root 访问权限，则可以遵照以下说明进入单用户模式：

1. 在重启 VM 时按“Esc”进入 GRUB
1. 在 GRUB 中，按“e”编辑你要启动进入的选定 OS（通常是第一行）
1. 找到内核行 - 在 Azure 中，这是以 `linux16` 开头的行
1. 按 Ctrl + E 转到行尾
1. 在行尾添加以下代码：`systemd.unit=rescue.target`
    * 如此即可启动进入单用户模式。 若要使用紧急模式，请将 `systemd.unit=emergency.target` 而不是 `systemd.unit=rescue.target` 添加到行尾
1. 按 Ctrl + X 退出，并使用应用的设置重新启动
1. 系统会提示输入管理员密码，以便能够进入单用户模式 - 这是根据前面的说明创建的同一密码

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>未在 RHEL 中启用 root 帐户的情况下进入单用户模式
如果未遵循上述步骤启用 root 用户，仍可以重置 root 密码。 请遵照以下说明：

> 注意:如果使用 SELinux，请确保在重置 root 密码时，执行[此处](https://aka.ms/rhel7grubterminal) Red Hat 文档中所述的附加步骤。

1. 在重启 VM 时按“Esc”进入 GRUB
1. 在 GRUB 中，按“e”编辑你要启动进入的选定 OS（通常是第一行）
1. 找到内核行 - 在 Azure 中，这是以 `linux16` 开头的行
1. 将 `rd.break` 添加到行尾，并确保在 `rd.break` 的前面插入一个空格（参阅以下示例）
    - 根据[此处](https://aka.ms/rhel7rootpassword) Red Hat 文档中所述，这会在将控制权从 `initramfs` 传递给 `systemd` 之前中断启动进程。
1. 按 Ctrl + X 退出，并使用应用的设置重新启动
1. 启动后，系统会将你置于紧急模式，此时打开的是只读文件系统。 在 shell 中输入 `mount -o remount,rw /sysroot`，以重新装载具有读/写权限的根文件系统
1. 启动进入单用户模式后，键入 `chroot /sysroot` 切换到 `sysroot` jail
1. 现在，你是 root 用户。 可以使用 `passwd` 重置 root 密码，然后遵照上面的说明进入单用户模式。 完成后，键入 `reboot -f` 以重新启动。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> 注意:遵照上面的整个说明会置于紧急 shell 中，因此还可以执行编辑 `fstab` 之类的任务。 但是，用户普遍接受的建议是重置 root 密码，并使用该密码进入单用户模式。


## <a name="access-for-centos"></a>在 CentOS 中访问
与 Red Hat Enterprise Linux 中的情况非常类似，CentOS 中的单用户模式也要求启用 GRUB 和 root 用户。

### <a name="grub-access-in-centos"></a>在 CentOS 中访问 GRUB
CentOS 原本就启用了 GRUB。 若要进入 GRUB，请使用 `sudo reboot` 重新启动 VM，然后按任意键。 此时会显示 GRUB 屏幕。

### <a name="single-user-mode-in-centos"></a>CentOS 中的单用户模式
遵照适用于 RHEL 的上述说明，在 CentOS 中启用单用户模式。

## <a name="access-for-ubuntu"></a>在 Ubuntu 中访问
Ubuntu 映像不需要 root 密码。 如果系统启动进入单用户模式，则你无需提供其他凭据即可使用它。

### <a name="grub-access-in-ubuntu"></a>在 Ubuntu 中访问 GRUB
若要访问 GRUB，请在启动 VM 时按住“Esc”。

默认情况下，Ubuntu 映像可能不会自动显示 GRUB 屏幕。 可按照以下说明对此进行更改：
1. 在所选的文本编辑器中打开 `/etc/default/grub.d/50-cloudimg-settings.cfg`
1. 将 `GRUB_TIMEOUT` 值更改为非零值
1. 在所选的文本编辑器中打开 `/etc/default/grub`
1. 注释掉 `GRUB_HIDDEN_TIMEOUT=1` 所在的行
1. 确保有一行显示`GRUB_TIMEOUT_STYLE=menu`
1. 运行 `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu 中的单用户模式
在无法正常启动的情况下，Ubuntu 会自动将你置于单用户模式。 若要手动进入单用户模式，请遵照以下说明：

1. 在 GRUB 中，按“e”编辑启动项（Ubuntu 项）
1. 查找以 `linux` 开头的行，然后查找 `ro`
1. 在 `ro` 的后面添加 `single`，请确保在 `single` 的前面和后面插入一个空格
1. 按 Ctrl + X 使用这些设置重新启动，并进入单用户模式

### <a name="using-grub-to-invoke-bash-in-ubuntu"></a>使用 GRUB 调用 Ubuntu 中的 Bash
可能存在尝试过上述说明后仍无法在 Ubuntu VM 中访问单用户模式的情况（例如忘记 root 密码）。 也可以指示内核将 /bin/bash 作为 init 运行，而不是作为系统 init，这将提供 Bash Shell 并允许进行系统维护。 请遵照以下说明：

1. 在 GRUB 中，按“e”编辑启动项（Ubuntu 项）
1. 查找以 `linux` 开头的行，然后查找 `ro`
1. 将 `ro` 替换为 `rw init=/bin/bash`
    - 此操作会将文件系统装载为读写并使用 /bin/bash 作为 init 进程
1. 按 Ctrl + X 使用这些设置重新启动

## <a name="access-for-coreos"></a>在 CoreOS 中访问
CoreOS 中的单用户模式要求启用 GRUB。

### <a name="grub-access-in-coreos"></a>在 CoreOS 中访问 GRUB
若要访问 GRUB，请在启动 VM 时按任意键。

### <a name="single-user-mode-in-coreos"></a>CoreOS 中的单用户模式
在无法正常启动的情况下，CoreOS 会自动将你置于单用户模式。 若要手动进入单用户模式，请遵照以下说明：
1. 在 GRUB 中，按“e”编辑启动项
1. 查找以 `linux$` 开头的行。 应该有 2 行，它们封装在不同的 if/else 子句中
1. 将 `coreos.autologin=ttyS0` 追加到两个 `linux$` 行的末尾
1. 按 Ctrl + X 使用这些设置重新启动，并进入单用户模式

## <a name="access-for-suse-sles"></a>在 SUSE SLES 中访问
如果系统已启动进入紧急模式，则 SLES 12 SP3+ 的较新映像允许通过串行控制台访问。

### <a name="grub-access-in-suse-sles"></a>在 SUSE SLES 中访问 GRUB
在 SLES 中访问 GRUB 需要通过 YaST 配置引导加载程序。 为此，请遵照以下说明操作：

1. 通过 SSH 连接到 SLES VM 并运行 `sudo yast bootloader`。 使用 `tab` 键、`enter` 键和箭头键浏览菜单。
1. 导航到 `Kernel Parameters`，并选中 `Use serial console`。
1. 将 `serial --unit=0 --speed=9600 --parity=no` 添加到 Console 参数

1. 按 F10 保存设置并退出
1. 若要进入 GRUB，请重新启动 VM，并在执行启动序列期间按任意键，使 GRUB 保留在屏幕上
    - GRUB 的默认超时为 1 秒。 可以通过更改 `/etc/default/grub` 中的 `GRUB_TIMEOUT` 变量来修改此超时

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES 中的单用户模式
如果 SLES 无法正常启动，则你会自动置于紧急 shell。 若要手动进入紧急 shell，请遵照以下说明操作：

1. 在 GRUB 中，按“e”编辑启动项（SLES 项）
1. 查找内核线-它将从`linux`
1. 在行尾追加 `systemd.unit=emergency.target`
1. 按 Ctrl + X 使用这些设置重新启动，并进入紧急 shell
   > 请注意，系统会将你置于采用只读文件系统的紧急 shell。 若要对任何文件进行任何编辑，需要使用读写权限重新装载文件系统。 为此，请在 shell 中输入 `mount -o remount,rw /`

## <a name="access-for-oracle-linux"></a>在 Oracle Linux 中访问
与 Red Hat Enterprise Linux 中的情况非常类似，Oracle Linux 中的单用户模式也要求启用 GRUB 和 root 用户。

### <a name="grub-access-in-oracle-linux"></a>在 Oracle Linux 中访问 GRUB
Oracle Linux 原本就启用了 GRUB。 若要进入 GRUB，请使用 `sudo reboot` 重新启动 VM，然后按“Esc”。 此时会显示 GRUB 屏幕。 如果看不到 GRUB, 请确保`GRUB_TERMINAL`行的值包含 "串行控制台", 如下所示:。 `GRUB_TERMINAL="serial console"` 重新生成 GRUB `grub2-mkconfig -o /boot/grub/grub.cfg`。

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux 中的单用户模式
遵照适用于 RHEL 的上述说明，在 Oracle Linux 中启用单用户模式。

## <a name="next-steps"></a>后续步骤
* 主要串行控制台 Linux 文档页位于[此处](serial-console-linux.md)。
* 了解如何使用串行控制台[在各种发行版中启用 GRUB](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* 使用串行控制台执行 [NMI 和 SysRq 调用](serial-console-nmi-sysrq.md)
* 串行控制台也适用于 [Windows](serial-console-windows.md) VM
* 详细了解[启动诊断](boot-diagnostics.md)
