---
title: 适用于 GRUB 和单用户模式的 Azure 串行控制台 |Microsoft Docs
description: 本文介绍如何在 Azure 虚拟机中对 GRUB 使用串行控制台。
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
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883925"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>使用串行控制台访问 GRUB 和单用户模式
在启动虚拟机（VM）时，可能会出现 "总计启动加载（GRUB）"。 由于它是在操作系统开始之前显示的，因此无法通过 SSH 访问 GRUB。 在 GRUB 中，可以修改启动配置，使其在单用户模式下启动。

单用户模式是最小功能的最小环境。 这对于调查启动问题、文件系统问题或网络问题可能非常有用。 较少的服务可在后台运行，根据运行级别的不同，文件系统甚至可能不会自动装载。

单用户模式也适用于以下情况： VM 可能配置为仅接受 SSH 密钥用于登录。 在这种情况下，您可能能够使用单用户模式创建具有密码身份验证的帐户。 

> [!NOTE]
> 串行控制台服务仅允许具有*参与者*级别或更高权限的用户访问 VM 的串行控制台。

若要进入单用户模式，请在 VM 启动时输入 GRUB，并在 GRUB 中修改启动配置。 请参阅下一部分中有关输入 GRUB 的详细说明。 通常，如果 VM 已配置为显示 GRUB，则可以使用 VM 的串行控制台中的 "重新启动" 按钮重新启动 VM 并显示 GRUB。

![Linux 串行控制台重新启动按钮](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>常规 GRUB 访问
若要访问 GRUB，请在串行控制台窗格处于打开状态时重新启动 VM。 某些分发版要求键盘输入显示 GRUB，其他分发版自动显示 GRUB 几秒钟，以允许用户键盘输入取消超时。

若要能够访问单用户模式，需要确保 VM 上已启用 GRUB。 根据你的分布情况，可能需要进行一些设置工作以确保启用了 GRUB。 有关特定于分发的信息，请参阅下一节和我们[对 Azure 上的 Linux 的支持](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)页。

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>重启 VM 以在串行控制台中访问 GRUB
在串行控制台中，可以通过将鼠标悬停在 "**重新启动**" 按钮，然后选择 "**重新启动 vm**" 来重新启动 vm。 有关重启的通知显示在窗格底部。

如果启用了[SysRq](./serial-console-nmi-sysrq.md) ，还可以通过运行 SysRq "b" 命令来重新启动 VM。 若要在重新启动时了解如何从 GRUB 获得哪些内容，请参阅下一节中的特定于分发的说明。

![Linux 串行控制台重启](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>一般单用户模式访问
如果尚未配置密码身份验证的帐户，则可能需要手动访问单用户模式。 修改 GRUB 配置以手动输入单用户模式。 完成此操作后，请参阅 "使用单用户模式重置或添加密码" 部分以获取进一步的说明。

如果 VM 无法启动，则分发经常会自动删除单用户模式或紧急模式。 但是，其他分发版需要额外的设置，例如设置根密码，然后才能将您自动置于单用户或紧急模式。

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>使用单用户模式重置或添加密码
在单用户模式下，通过执行以下操作来添加具有 sudo 权限的新用户：
1. 运行`useradd <username>`以添加用户。
1. 运行`sudo usermod -a -G sudo <username>`以授予新用户 root 权限。
1. 使用 `passwd <username>` 为新用户设置密码。 然后，你可以以新用户身份登录。


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>在 Red Hat Enterprise Linux (RHEL) 中访问
如果 RHEL 无法正常启动，它会自动进入单用户模式。 但是，如果尚未为单用户模式设置根访问权限，则没有根密码，无法登录。 有一种解决方法（请参阅 "在 RHEL 中手动输入单用户模式" 部分），但建议最初设置根访问。

### <a name="grub-access-in-rhel"></a>在 RHEL 中访问 GRUB 访问
RHEL 原本就启用了 GRUB。 若要进入 GRUB，请运行`sudo reboot`来重新启动 VM，然后按任意键。 应显示 GRUB 窗格。 如果不是这样，请确保 GRUB 文件（`/etc/default/grub`）中存在以下行：

**适用于 RHEL 8**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**适用于 RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat 还提供了用于在修复模式、紧急模式或调试模式下启动以及用于重置根密码的文档。 有关说明，请参阅[启动过程中的终端菜单编辑](https://aka.ms/rhel7grubterminal)。

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>为 RHEL 中的单用户模式设置根访问权限
默认情况下，根用户处于禁用状态。 RHEL 中的单用户模式要求启用根用户。 如果需要启用单用户模式，请使用以下说明：

1. 通过 SSH 登录 Red Hat 系统。
1. 切换到根。
1. 通过执行以下操作为根用户启用密码：
    * 运行`passwd root` （设置强根密码）。
1. 通过执行以下操作，确保根用户只能通过 ttyS0 登录：  
    a. 运行`edit /etc/ssh/sshd_config`，并确保 PermitRootLogIn 设置为`no`。  
    b. 运行`edit /etc/securetty file`以仅允许通过 ttyS0 登录。

现在，如果系统启动到单用户模式，则可以用 root 密码登录。

或者，对于 RHEL 7.4 + 或 6.9 +，若要在 GRUB 提示中启用单用户模式，请参阅[启动到单用户模式](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)。

### <a name="manually-enter-single-user-mode-in-rhel"></a>在 RHEL 中手动输入单用户模式
如果已按照前面的说明设置了 GRUB 和根访问，则可通过执行以下操作进入单用户模式：

1. 若要进入 GRUB，请在重新启动 VM 时按 Esc。
1. 在 GRUB 中，按 E 编辑要启动的操作系统。 操作系统通常在第一行列出。
1. 查找 "内核" 行。 在 Azure 中，它从*linux16*开始。
1. 按 Ctrl + E 以前往行的末尾。
1. 在行的末尾，添加 " *systemd*"。
    
    此操作可引导你进入单用户模式。 如果要使用紧急模式，请将*systemd = "紧急*" 添加到行的末尾（而不是*systemd =* ""。

1. 按 Ctrl + X 退出并重新启动应用的设置。

   系统将提示你输入管理员密码，然后才能进入单用户模式。 此密码是你在前面的说明中创建的密码。

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>输入 RHEL 中未启用根帐户的单用户模式
如果未按前面的说明启用 root 用户，则仍可通过执行以下操作来重置根密码：

> [!NOTE]
> 如果你使用的是 SELinux，则在重置根密码时，请确保按照[Red Hat 文档](https://aka.ms/rhel7grubterminal)中所述的其他步骤进行操作。

1. 若要进入 GRUB，请在重新启动 VM 时按 Esc。

1. 在 GRUB 中，按 E 编辑要启动的操作系统。 操作系统通常在第一行列出。
1. 查找 "内核" 行。 在 Azure 中，它从*linux16*开始。
1. 在行的末尾，将 " *rd* " 添加到行的末尾。 在内核行与*rd*之间留有一个空格。

    如[Red Hat 文档](https://aka.ms/rhel7rootpassword)中所述，此操作会`initramfs`在`systemd`将控制传递到之前中断启动进程。
1. 按 Ctrl + X 退出并重新启动应用的设置。

   重新启动后，系统会将其放入具有只读文件系统的紧急模式。 
   
1. 在 shell 中，输入`mount -o remount,rw /sysroot`以将根文件系统重新装载为读/写权限。
1. 启动到单用户模式后，输入`chroot /sysroot`以切换`sysroot`到越狱。
1. 你现在处于 root 状态。 您可以通过输入`passwd`并使用前面的说明来输入单用户模式来重置 root 密码。 
1. 完成后，输入`reboot -f`重新启动。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> 通过上述说明运行将会使你进入紧急 shell，以便你还可以执行编辑`fstab`等任务。 但是，我们通常建议你重置根密码，并使用它来进入单用户模式。

## <a name="access-for-centos"></a>在 CentOS 中访问
与 Red Hat Enterprise Linux 一样，CentOS 中的单用户模式需要有 GRUB 并且要启用根用户。

### <a name="grub-access-in-centos"></a>在 CentOS 中访问 GRUB
CentOS 原本就启用了 GRUB。 若要进入 GRUB，请输入`sudo reboot`，然后按任意键重新启动 VM。 此操作将显示 GRUB 窗格。

### <a name="single-user-mode-in-centos"></a>CentOS 中的单用户模式
若要在 CentOS 中启用单用户模式，请遵循 RHEL 前面的说明。

## <a name="access-for-ubuntu"></a>在 Ubuntu 中访问
Ubuntu 映像不需要 root 密码。 如果系统启动到单用户模式，则可以使用它，而无需其他凭据。

### <a name="grub-access-in-ubuntu"></a>在 Ubuntu 中访问 GRUB
若要访问 GRUB，请在 VM 启动时按 Esc 键。

默认情况下，Ubuntu 映像可能不会自动显示 GRUB 窗格。 可以通过执行以下操作来更改设置：
1. 在文本编辑器中，打开 */etc/default/grub.d/50-cloudimg-settings.cfg*文件。

1. `GRUB_TIMEOUT`将值更改为非零值。
1. 在文本编辑器中，打开 */etc/default/grub*。
1. 注释掉`GRUB_HIDDEN_TIMEOUT=1`该行。
1. 请确保有`GRUB_TIMEOUT_STYLE=menu`一行。
1. 运行 `sudo update-grub`。

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu 中的单用户模式
如果 Ubuntu 无法正常启动，它会自动进入单用户模式。 若要手动输入单用户模式，请执行以下操作：

1. 在 GRUB 中，按 E 编辑启动条目（Ubuntu 条目）。
1. 查找以*linux*开头的行，然后查找 " *ro*"。
1. 在*ro*后添加*single* ，确保*单个*空格前后有一个空格。
1. 按下 Ctrl + X 可通过这些设置重新启动并进入单用户模式。

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>使用 GRUB 在 Ubuntu 中调用 bash
尝试上述说明后，可能会有某种情况（如忘记密码），在 Ubuntu VM 中仍无法访问单用户模式。 你还可以将内核指示为作为`/bin/bash` init 运行，而不是在系统 init 中运行。 此操作可提供 bash shell 并允许系统维护。 请遵照以下说明：

1. 在 GRUB 中，按 E 编辑启动条目（Ubuntu 条目）。

1. 查找以*linux*开头的行，然后查找 " *ro*"。
1. 将*ro*替换*为 rw init =/bin/bash*。

    此操作会将文件系统作为读写方式装载， `/bin/bash`并使用作为 init 进程。
1. 按下 Ctrl + X 可通过这些设置重新启动。

## <a name="access-for-coreos"></a>在 CoreOS 中访问
CoreOS 中的单用户模式需要启用 GRUB。

### <a name="grub-access-in-coreos"></a>在 CoreOS 中访问 GRUB
若要访问 GRUB，请在 VM 启动时按任意键。

### <a name="single-user-mode-in-coreos"></a>CoreOS 中的单用户模式
如果 CoreOS 无法正常启动，它会自动进入单用户模式。 若要手动输入单用户模式，请执行以下操作：

1. 在 GRUB 中，按 E 编辑启动项。

1. 查找以*linux $* 开头的行。 应有两个行实例，每个实例封装在不同的*if .。。else*子句。
1. 将*coreos autologin = ttyS0*追加到每个*linux $* line 的末尾。
1. 按下 Ctrl + X 可通过这些设置重新启动并进入单用户模式。

## <a name="access-for-suse-sles"></a>在 SUSE SLES 中访问
如果系统启动到紧急模式，则 SLES 12 SP3 + 的较新映像会允许通过串行控制台进行访问。

### <a name="grub-access-in-suse-sles"></a>在 SUSE SLES 中访问 GRUB
SLES 中的 GRUB 访问需要通过 YaST 进行加载配置。 若要创建配置，请执行以下操作：

1. 使用 SSH 登录到 SLES VM，然后运行`sudo yast bootloader`。 按 Tab，按 Enter，然后使用箭头键在菜单中导航。

1. 中转到 "**内核参数**"，然后选中 "**使用串行控制台**" 复选框。
1. 向`serial --unit=0 --speed=9600 --parity=no` **控制台**参数中添加。
1. 按 F10 保存设置并退出。
1. 若要进入 GRUB，请重新启动 VM，并在启动顺序中按任意键，以使 GRUB 窗格显示。

    GRUB 的默认超时值为**1**。 可以通过更改`GRUB_TIMEOUT` */etc/default/grub*文件中的变量来修改此设置。

![初始化引导加载配置](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES 中的单用户模式
如果 SLES 无法正常启动，会自动将其放入紧急外壳。 若要手动输入紧急 shell，请执行以下操作：

1. 在 GRUB 中，按 E 编辑启动条目（SLES 条目）。

1. 查找以*linux*开头的内核行。
1. 将*systemd = "紧急*" 添加到内核行的末尾。
1. 按下 Ctrl + X 可通过这些设置重新启动并进入紧急外壳。

   > [!NOTE]
   > 此操作会将你带到具有只读文件系统的紧急外壳。 若要编辑任何文件，请重新装入具有读写权限的文件系统。 为此，请在`mount -o remount,rw /` shell 中输入。

## <a name="access-for-oracle-linux"></a>在 Oracle Linux 中访问
与 Red Hat Enterprise Linux 一样，Oracle Linux 中的单用户模式需要启用 GRUB 和根用户。

### <a name="grub-access-in-oracle-linux"></a>在 Oracle Linux 中访问 GRUB
Oracle Linux 原本就启用了 GRUB。 若要进入 GRUB，请运行`sudo reboot`来重新启动 VM，然后按 Esc。此操作将显示 GRUB 窗格。 如果未显示 GRUB 窗格，请确保`GRUB_TERMINAL`行的值包含*串行控制台* `GRUB_TERMINAL="serial console"`（即）。 重新生成 GRUB `grub2-mkconfig -o /boot/grub/grub.cfg`。

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux 中的单用户模式
若要在 Oracle Linux 中启用单用户模式，请遵循 RHEL 前面的说明。

## <a name="next-steps"></a>后续步骤
若要详细了解串行控制台，请参阅：
* [Linux 串行控制台文档](serial-console-linux.md)
* [使用串行控制台在各种分发中启用 GRUB](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [对 NMI 和 SysRq 调用使用串行控制台](serial-console-nmi-sysrq.md)
* [适用于 Windows Vm 的串行控制台](serial-console-windows.md)
* [启动诊断](boot-diagnostics.md)
