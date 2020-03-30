---
title: 用于 GRUB 和单用户模式的 Azure 串行控制台 |微软文档
description: 本文介绍如何在 Azure 虚拟机中使用串行控制台进行 GRUB。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883925"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>使用串行控制台访问 GRUB 和单用户模式
在启动虚拟机 （VM） 时，GRand 统一引导加载程序 （GRUB） 可能是您首先看到的事情。 由于它在操作系统启动之前显示，因此无法通过 SSH 访问 GRUB。 在 GRUB 中，您可以修改引导配置以引导到单用户模式等。

单用户模式是一个功能最少的最低环境。 它可用于调查引导问题、文件系统问题或网络问题。 在后台运行的服务较少，并且根据运行级别，文件系统甚至可能无法自动装载。

在 VM 可能配置为仅接受 SSH 密钥进行登录的情况下，单用户模式也很有用。 在这种情况下，您可能能够使用单用户模式创建具有密码身份验证的帐户。 

> [!NOTE]
> 串行控制台服务仅允许具有*参与者*级别或更高权限的用户访问 VM 的串行控制台。

要进入单用户模式，请在 VM 启动时进入 GRUB，并在 GRUB 中修改启动配置。 在下一节中查看有关输入 GRUB 的详细说明。 通常，如果 VM 已配置为显示 GRUB，则可以使用 VM 串行控制台中的重新启动按钮重新启动 VM 并显示 GRUB。

![Linux 串行控制台重新启动按钮](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>常规 GRUB 访问
要访问 GRUB，请在串行控制台窗格打开时重新启动 VM。 某些发行版需要键盘输入来显示 GRUB，而其他发行版会自动显示 GRUB 几秒钟，以允许用户键盘输入取消超时。

为了能够访问单用户模式，您需要确保在 VM 上启用了 GRUB。 根据您的分布，可能需要进行一些设置工作，以确保启用 GRUB。 有关特定于分发的信息，请参阅 Azure 页面上的下一节和我们对[Linux 的支持](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)。

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>重启 VM 以在串行控制台中访问 GRUB
您可以通过将鼠标悬停在 **"重新启动"** 按钮，然后选择 **"重新启动 VM"，** 在串行控制台中重新启动 VM。 有关重新启动的通知显示在窗格的底部。

如果[启用了 SysRq，](./serial-console-nmi-sysrq.md)还可以通过运行 SysRq"b"命令重新启动 VM。 要了解重新启动时对 GRUB 的期望，请参阅下一节中特定于分发的说明。

![Linux 串行控制台重新启动](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>一般单用户模式访问
如果尚未配置具有密码身份验证的帐户，则可能需要手动访问单用户模式。 修改 GRUB 配置以手动进入单用户模式。 完成此操作后，请参阅"使用单用户模式重置或添加密码"部分，了解进一步说明。

如果 VM 无法启动，分发通常会自动将您放入单用户模式或紧急模式。 但是，其他发行版需要额外的设置，例如设置根密码，然后才能自动将您放入单用户或紧急模式。

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>使用单用户模式重置或添加密码
在单用户模式下后，通过执行以下操作添加具有 sudo 权限的新用户：
1. 运行`useradd <username>`以添加用户。
1. 运行`sudo usermod -a -G sudo <username>`以授予新用户根权限。
1. 使用 `passwd <username>` 为新用户设置密码。 然后，您可以以新用户身份登录。


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>在 Red Hat Enterprise Linux (RHEL) 中访问
如果 RHEL 无法正常启动，它会自动将您放入单用户模式。 但是，如果您尚未为单用户模式设置根访问权限，则没有根密码，无法登录。 有一个解决方法（请参阅 RHEL 中的"手动输入单用户模式"部分），但我们建议您首先设置根访问。

### <a name="grub-access-in-rhel"></a>在 RHEL 中访问 GRUB 访问
RHEL 原本就启用了 GRUB。 要进入 GRUB，请通过 运行`sudo reboot`重新启动 VM，然后按任意键。 应显示 GRUB 窗格。 如果不是，请确保以下行存在于您的 GRUB 文件中 （`/etc/default/grub`：

**用于 RHEL 8**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**用于 RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> 红帽还提供引导到救援模式、紧急模式或调试模式以及重置根密码的文档。 有关说明，请参阅[引导期间编辑终端菜单](https://aka.ms/rhel7grubterminal)。

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>在 RHEL 中为单用户模式设置根访问
默认情况下，根用户已禁用。 RHEL 中的单用户模式需要启用根用户。 如果需要启用单用户模式，请使用以下说明：

1. 通过 SSH 登录红帽系统。
1. 切换到根目录。
1. 通过执行以下操作为根用户启用密码：
    * 运行`passwd root`（设置强根密码）。
1. 确保根用户只能通过 ttyS0 登录，只需执行以下操作：  
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 运行`edit /etc/ssh/sshd_config`，并确保允许 RootLogIn 设置为`no`。  
    b.保留“数据库类型”设置，即设置为“共享”。 运行`edit /etc/securetty file`以仅通过 ttyS0 允许登录。

现在，如果系统引导到单用户模式，您可以使用根密码登录。

或者，对于 RHEL 7.4+ 或 6.9+，要在 GRUB 提示符中启用单用户模式，请参阅[引导到单用户模式](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)。

### <a name="manually-enter-single-user-mode-in-rhel"></a>在 RHEL 中手动进入单用户模式
如果使用上述说明设置 GRUB 和根访问，则可以通过执行以下操作进入单用户模式：

1. 要进入 GRUB，请在重新启动 VM 时按 Esc。
1. 在 GRUB 中，按 E 编辑要引导到的操作系统。 操作系统通常列在第一行。
1. 查找内核行。 在 Azure 中，它从*linux16*开始。
1. 按 Ctrl+E 转到行尾。
1. 在行尾，添加*系统.unit_rescue.目标*。
    
    此操作将引导您进入单用户模式。 如果要使用紧急模式，请使用*系统.unit_emergency.target*添加到线路的末尾（而不是*系统.unit_rescue.target）。*

1. 按 Ctrl+X 退出并重新启动应用的设置。

   在输入单用户模式之前，系统将提示您输入管理员密码。 此密码是您在前面的说明中创建的密码。

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>在 RHEL 中未启用根帐户的情况下输入单用户模式
如果未按照前面的说明启用根用户，您仍可以通过执行以下操作来重置根密码：

> [!NOTE]
> 如果您使用的是 SELinux，则重置根密码时，请务必按照[红帽文档中](https://aka.ms/rhel7grubterminal)介绍的其他步骤操作。

1. 要进入 GRUB，请在重新启动 VM 时按 Esc。

1. 在 GRUB 中，按 E 编辑要引导到的操作系统。 操作系统通常列在第一行。
1. 查找内核行。 在 Azure 中，它从*linux16*开始。
1. 在行的末尾，将*rd.break*添加到行的末尾。 在内核行和*rd.break*之间留出一个空格。

    此操作在将控件传递到`initramfs`之前中断启动过程`systemd`，如[红帽文档中](https://aka.ms/rhel7rootpassword)所述。
1. 按 Ctrl+X 退出并重新启动应用的设置。

   重新启动后，您将使用只读文件系统进入紧急模式。 
   
1. 在 shell 中`mount -o remount,rw /sysroot`，输入以重新装入具有读/写权限的根文件系统。
1. 启动到单用户模式后，进入`chroot /sysroot`以切换到`sysroot`监狱。
1. 你现在是根。 您可以通过输入`passwd`然后使用上述说明来重置根密码，从而进入单用户模式。 
1. 完成后，进入`reboot -f`以重新启动。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> 运行前面的说明会将您放入紧急 shell，以便您还可以执行编辑等任务`fstab`。 但是，我们通常建议您重置根密码，并用它来进入单用户模式。

## <a name="access-for-centos"></a>在 CentOS 中访问
与红帽企业 Linux 一样，CentOS 中的单用户模式需要 GRUB 和根用户启用。

### <a name="grub-access-in-centos"></a>在 CentOS 中访问 GRUB
CentOS 原本就启用了 GRUB。 要进入 GRUB，请通过输入`sudo reboot`重新启动 VM，然后按任意键。 此操作将显示 GRUB 窗格。

### <a name="single-user-mode-in-centos"></a>CentOS 中的单用户模式
要在 CentOS 中启用单用户模式，请按照 RHEL 的较早说明操作。

## <a name="access-for-ubuntu"></a>在 Ubuntu 中访问
Ubuntu 图像不需要根密码。 如果系统启动到单用户模式，则可以不使用其他凭据使用它。

### <a name="grub-access-in-ubuntu"></a>在 Ubuntu 中访问 GRUB
要访问 GRUB，请按住在 VM 启动时按住 Esc。

默认情况下，Ubuntu 图像可能不会自动显示 GRUB 窗格。 您可以通过执行以下操作来更改设置：
1. 在文本编辑器中，打开 */etc/default/grub.d/50-云-设置.cfg*文件。

1. 将`GRUB_TIMEOUT`值更改为非零值。
1. 在文本编辑器中，打开 */etc/默认/grub*。
1. 注释掉行`GRUB_HIDDEN_TIMEOUT=1`。
1. 确保有一`GRUB_TIMEOUT_STYLE=menu`条线。
1. 运行 `sudo update-grub`。

### <a name="single-user-mode-in-ubuntu"></a>乌本图中的单用户模式
如果 Ubuntu 无法正常启动，它会自动将您放入单用户模式。 要手动进入单用户模式，请执行以下操作：

1. 在 GRUB 中，按 E 编辑引导条目（Ubuntu 条目）。
1. 寻找从*linux*开始的行，然后寻找*ro。*
1. 在*ro*之后添加*单*，确保*单*前后有一个空格。
1. 按 Ctrl+X 使用这些设置重新启动并进入单用户模式。

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>使用 GRUB 在 Ubuntu 中调用 bash
尝试上述说明后，可能会出现以下情况（如忘记的根密码），您仍无法访问 Ubuntu VM 中的单用户模式。 您还可以告诉内核以 init`/bin/bash`身份运行，而不是系统 init。 此操作为您提供了一个 bash 外壳，并允许进行系统维护。 请遵照以下说明：

1. 在 GRUB 中，按 E 编辑引导条目（Ubuntu 条目）。

1. 寻找从*linux*开始的行，然后寻找*ro。*
1. 用*rw init_/bin/bash*替换*ro。*

    此操作将文件系统装载为读写，并用作`/bin/bash`init 进程。
1. 按 Ctrl+X 可使用这些设置重新启动。

## <a name="access-for-coreos"></a>在 CoreOS 中访问
CoreOS 中的单用户模式需要启用 GRUB。

### <a name="grub-access-in-coreos"></a>在 CoreOS 中访问 GRUB
要访问 GRUB，请按 VM 启动时的任何键。

### <a name="single-user-mode-in-coreos"></a>CoreOS 中的单用户模式
如果 CoreOS 无法正常启动，它会自动将您放入单用户模式。 要手动进入单用户模式，请执行以下操作：

1. 在 GRUB 中，按 E 编辑引导条目。

1. 寻找以*linux$* 开头的行。 行应该有两个实例，每个实例封装在不同的*if...否则*条款。
1. 将*coreos.autologin_ttyS0*追加到每个*linux$* 行的末尾。
1. 按 Ctrl+X 使用这些设置重新启动并进入单用户模式。

## <a name="access-for-suse-sles"></a>在 SUSE SLES 中访问
如果系统进入紧急模式，SLES 12 SP3+ 的较新映像允许通过串行控制台进行访问。

### <a name="grub-access-in-suse-sles"></a>在 SUSE SLES 中访问 GRUB
SLES 中的 GRUB 访问需要通过 YaST 进行引导加载程序配置。 要创建配置，请执行以下操作：

1. 使用 SSH 登录到 SLES VM，然后运行`sudo yast bootloader`。 按"选项卡"，按 Enter，然后使用箭头键在菜单中导航。

1. 转到**内核参数**，然后选择 **"使用串行控制台**"复选框。
1. 添加到`serial --unit=0 --speed=9600 --parity=no`**控制台**参数。
1. 按 F10 可保存设置和退出。
1. 要进入 GRUB，请重新启动 VM，并在启动序列中按任意键以保持 GRUB 窗格的显示。

    GRUB 的默认超时为**1。** 您可以通过更改`GRUB_TIMEOUT`*/etc/default/grub*文件中的变量来修改此设置。

![初始化引导加载程序配置](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES 中的单用户模式
如果 SLES 无法正常启动，则会自动将您放入紧急外壳中。 要手动输入紧急外壳，请执行以下操作：

1. 在 GRUB 中，按 E 编辑引导条目（SLES 条目）。

1. 查找以*linux*开头的内核行。
1. 追加*系统.unit_紧急.目标*到内核行的末尾。
1. 按 Ctrl+X 使用这些设置重新启动并输入紧急外壳。

   > [!NOTE]
   > 此操作将您放入具有只读文件系统的紧急外壳中。 要编辑任何文件，请重新装入具有读写权限的文件系统。 为此，请输入`mount -o remount,rw /`壳体。

## <a name="access-for-oracle-linux"></a>在 Oracle Linux 中访问
与红帽企业 Linux 一样，Oracle Linux 中的单用户模式需要启用 GRUB 和根用户。

### <a name="grub-access-in-oracle-linux"></a>在 Oracle Linux 中访问 GRUB
Oracle Linux 原本就启用了 GRUB。 要进入 GRUB，请通过运行`sudo reboot`重新启动 VM，然后按 Esc。此操作将显示 GRUB 窗格。 如果未显示 GRUB 窗格，请确保`GRUB_TERMINAL`行的值包含*串行控制台*（即 ）。 `GRUB_TERMINAL="serial console"` 使用`grub2-mkconfig -o /boot/grub/grub.cfg`重建 GRUB。

### <a name="single-user-mode-in-oracle-linux"></a>甲骨文Linux中的单用户模式
要在 Oracle Linux 中启用单用户模式，请按照 RHEL 的较早说明操作。

## <a name="next-steps"></a>后续步骤
要了解有关串行控制台的信息，请参阅：
* [Linux 串行控制台文档](serial-console-linux.md)
* [使用串行控制台在各种发行版中启用 GRUB](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [将串行控制台用于 NMI 和 SysRq 调用](serial-console-nmi-sysrq.md)
* [适用于 Windows VM 的串行控制台](serial-console-windows.md)
* [启动诊断](boot-diagnostics.md)
