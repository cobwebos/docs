---
title: 适用于 Linux 的 Azure 串行控制台 |Microsoft Docs
description: 使用 Linux 示例为 Azure 虚拟机和虚拟机规模集 Bi-Directional 串行控制台。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 25e3a9cb363ae4e64b953aeb7a6da4e2e66c9fc7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977092"
---
# <a name="azure-serial-console-for-linux"></a>适用于 Linux 的 Azure 串行控制台

使用 Azure 门户中的串行控制台，可以访问适用于 Linux 虚拟机的基于文本的控制台 (Vm) 和虚拟机规模集实例。 此串行连接连接到 VM 或虚拟机规模集实例的 ttys0 串行端口，提供与网络或操作系统状态无关的访问权限。 串行控制台只能通过使用 Azure 门户来访问，并且仅供对 VM 或虚拟机规模集具有参与者访问角色或更高权限的用户使用。

串行控制台的工作方式与 VM 和虚拟机规模集实例的工作方式相同。 在本文档中，除非另有说明，否则所有对 VM 的提及都将隐式包含虚拟机规模集实例。

在全球 Azure 区域和 Azure 政府公共预览版中，串行控制台已正式发布。 它在 Azure 中国云中尚不可用。

有关适用于 Windows 的串行控制台文档，请参阅 [Windows 串行控制台](./serial-console-windows.md)。

> [!NOTE]
> 串行控制台当前与托管的启动诊断存储帐户不兼容。 若要使用串行控制台，请确保使用的是自定义的存储帐户。

## <a name="prerequisites"></a>先决条件

- VM 或虚拟机规模集实例必须使用资源管理器部署模型。 不支持经典部署。

- 使用串行控制台的 Azure 帐户必须对 VM 和[启动诊断](boot-diagnostics.md)存储帐户拥有[虚拟机参与者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

- VM 或虚拟机规模集实例必须具有基于密码的用户。 可以使用 VM 访问扩展的[重置密码](../extensions/vmaccess.md#reset-password)功能创建一个帐户。 在“支持 + 故障排除”部分选择“重置密码”。 

- VM 或虚拟机规模集实例必须已启用 [启动诊断](boot-diagnostics.md) 。

    ![启动诊断设置](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- 有关特定于 Linux 分发版的设置，请参阅[串行控制台 Linux 分发版可用性](#serial-console-linux-distribution-availability)。

- 必须为虚拟机或虚拟机规模集实例配置 "串行输出" `ttys0` 。 这是 Azure 映像的默认设置，但你需要在自定义映像上仔细检查此设置。 详细信息 [如下](#custom-linux-images)。


> [!NOTE]
> 串行控制台需要一个配置了密码的本地用户。 仅使用 SSH 公钥配置的 Vm 或虚拟机规模集将无法登录到串行控制台。 若要创建带有密码的本地用户，请使用 [VMAccess 扩展](../extensions/vmaccess.md)（在 Azure 门户中选择“重置密码”即可访问它），然后创建带有密码的本地用户。****
> 也可在帐户中重置管理员密码，方法是[使用 GRUB 启动进入单用户模式](./serial-console-grub-single-user-mode.md)。

## <a name="serial-console-linux-distribution-availability"></a>串行控制台 Linux 发行版可用性
要使串行控制台正常运行，必须将来宾操作系统配置为向串行端口读取和写入控制台消息。 大多数 [认可的 Azure Linux 发行](../linux/endorsed-distros.md) 版都默认配置了串行控制台。 在 Azure 门户的“支持 + 故障排除”部分选择“串行控制台”即可访问串行控制台。********

> [!NOTE]
> 如果在串行控制台中没有看到任何内容，请确保在 VM 上启用了启动诊断。 命中 **输入** 通常会修复串行控制台中没有显示任何内容的问题。

分发      | 串行控制台访问
:-----------|:---------------------
Red Hat Enterprise Linux    | 默认已启用串行控制台访问。
CentOS      | 默认已启用串行控制台访问。
Debian      | 默认已启用串行控制台访问。
Ubuntu      | 默认已启用串行控制台访问。
CoreOS      | 默认已启用串行控制台访问。
SUSE        | Azure 中提供的较新 SLES 映像默认已启用串行控制台访问。 如果在 Azure 上使用旧版（10 或更低）SLES，请参阅此[知识库文章](https://www.novell.com/support/kb/doc.php?id=3456486)启用串行控制台。
Oracle Linux        | 默认已启用串行控制台访问。

### <a name="custom-linux-images"></a>自定义 Linux 映像
若要为自定义 Linux VM 映像启用串行控制台，请在文件 */etc/inittab* 中启用控制台访问，以便在 `ttyS0` 上运行终端。 例如：`S0:12345:respawn:/sbin/agetty -L 115200 console vt102`。 还可能需要在 ttyS0 上生成 getty。 这可以通过来实现 `systemctl start serial-getty@ttyS0.service` 。

还需要将 ttys0 添加为串行输出的目标。 有关配置自定义映像以使用串行控制台的详细信息，请参阅在 [Azure 中创建和上载 LINUX VHD 中](../linux/create-upload-generic.md#general-linux-system-requirements)的常规系统要求。

如果你正在生成自定义内核，请考虑启用以下内核标志：`CONFIG_SERIAL_8250=y` 和 `CONFIG_MAGIC_SYSRQ_SERIAL=y`。 配置文件通常位于 */boot/* 路径。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>访问串行控制台的常见方案

方案          | 串行控制台中的操作
:------------------|:-----------------------------------------
损坏的 *FSTAB* 文件 | 按 **Enter** 键继续，然后使用文本编辑器修复 *FSTAB* 文件。 可能需要在单用户模式下执行此操作。 有关详细信息，请参阅 [如何修复 fstab 问题](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) 和 [使用串行控制台访问 GRUB 和单一用户模式](serial-console-grub-single-user-mode.md)的串行控制台部分。
错误的防火墙规则 |  如果已将 iptables 配置为阻止 SSH 连接，则可以使用串行控制台与 VM 交互，而无需使用 SSH。 可在 [iptables 手册页](https://linux.die.net/man/8/iptables)找到更多详细信息。<br>同样，如果你的 firewalld 阻止 SSH 访问，你可以通过串行控制台访问 VM，然后重新配置 firewalld。 可在 [firewalld 文档](https://firewalld.org/documentation/)中找到更多详细信息。
文件系统损坏/检查 | 有关使用串行控制台排查文件系统损坏的详细信息，请参阅 Azure Linux VM 的串行控制台部分 [无法启动，原因是文件系统错误](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 。
SSH 配置问题 | 访问串行控制台并更改设置。 无论 VM 的 SSH 配置如何，串行控制台都可以使用，因为它不需要 VM 使用网络连接。 故障排除指南可用于 [排查连接失败、错误或被拒绝的 Azure LINUX VM 的 SSH 连接问题](./troubleshoot-ssh-connection.md)。 [有关连接到 Azure 中的 LINUX VM 时出现的问题的详细 SSH 故障排除步骤](./detailed-troubleshoot-ssh-connection.md)，请参阅详细信息
与引导加载程序交互 | 从串行控制台边栏选项卡中重启 VM 以访问 Linux VM 上的 GRUB。 有关详细信息和发行版特定的信息，请参阅 [使用串行控制台访问 GRUB 和单用户模式](serial-console-grub-single-user-mode.md)。

## <a name="disable-the-serial-console"></a>禁用串行控制台

默认情况下，所有订阅都启用了串行控制台访问。 可以在订阅级别或 VM/虚拟机规模集级别禁用串行控制台。 有关详细说明，请访问[启用和禁用 Azure 串行控制台](./serial-console-enable-disable.md)。

## <a name="serial-console-security"></a>串行控制台安全性

### <a name="access-security"></a>访问安全性
只允许对虚拟机拥有[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高权限访问角色的用户访问串行控制台。 如果 Azure Active Directory 租户需要多重身份验证 (MFA)，则访问串行控制台时也需要执行 MFA，因为串行控制台是通过 [Azure 门户](https://portal.azure.com)访问的。

### <a name="channel-security"></a>通道安全性
来回发送的所有数据在线路上经过加密。

### <a name="audit-logs"></a>审核日志
对串行控制台的所有访问目前都会记录在虚拟机的[启动诊断](./boot-diagnostics.md)日志中。 Azure 虚拟机管理员拥有并可控制这些日志的访问权限。

> [!CAUTION]
> 不会记录控制台的访问密码。 但是，如果在控制台中运行的命令包含或输出密码、机密、用户名或其他任何形式的个人身份信息 (PII)，则这些信息将写入到 VM 启动诊断日志。 这些信息是在实现串行控制台的回滚功能过程中连同其他所有可见文本一起写入的。 这些日志不断循环，只有对诊断存储帐户拥有读取权限的个人才能访问它们。 如果要输入包含机密或 PII 的任何 dataor 命令，则建议使用 SSH，除非绝对需要串行控制台。

### <a name="concurrent-usage"></a>并发使用
如果某个用户已连接到串行控制台，而另一个用户已成功请求访问同一个虚拟机，则第一个用户将断开连接，第二用户此时已连接到同一会话。

> [!CAUTION]
> 这意味着不会注销已断开连接的用户。当使用 SIGHUP 或类似的机制) 时，通过使用或类似的机制强制注销 (的能力仍在路线图上。 对于 Windows，特殊管理控制台 (SAC) 中会启用自动超时；但对于 Linux，可以配置终端超时设置。 为此，请将 `export TMOUT=600` 添加到用于登录控制台的用户的 *.bash_profile* 或 *.profile* 中。 此设置使会话在 10 分钟后超时。

## <a name="accessibility"></a>可访问性
辅助功能是 Azure 串行控制台的主要焦点。 为此，我们会确保串行控制台的完全可访问性。

### <a name="keyboard-navigation"></a>键盘导航
使用键盘上的 **Tab** 键在 Azure 门户中的串行控制台界面上导航。 屏幕上会突出显示你的位置。 若要使焦点离开串行控制台窗口，请在键盘上按 **Ctrl**+**F6**。

### <a name="use-serial-console-with-a-screen-reader"></a>通过屏幕阅读器使用串行控制台
串行控制台内置了屏幕阅读器支持。 在打开屏幕阅读器的情况下导航，屏幕阅读器可大声读出当前所选按钮的替换文字。

## <a name="known-issues"></a>已知问题
我们注意到，串行控制台和 VM 的操作系统存在一些问题。 下面列出了这些问题以及针对 Linux Vm 的缓解步骤。 这些问题和缓解措施既适用于 VM，又适用于虚拟机规模集实例。 如果这些错误与你看到的错误不匹配，请参阅[常见串行控制台错误](./serial-console-errors.md)处的常见串行控制台服务错误。

问题                           |   缓解操作
:---------------------------------|:--------------------------------------------|
在出现连接标题后按 **Enter** 不会显示登录提示。 | 可能未正确配置 GRUB。 运行以下命令： `grub2-mkconfig -o /etc/grub2-efi.cfg` 和/或 `grub2-mkconfig -o /etc/grub2.cfg` 。 有关详细信息，请参阅[按 Enter 不起任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)。 如果你运行的是自定义 VM、强化的设备或 GRUB 配置，导致 Linux 无法连接到串行端口，则可能出现此问题。
串行控制台文本仅占用屏幕大小的一部分（通常在使用文本编辑器后）。 | 串行控制台不支持协商窗口大小 ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt))，这意味着不会发送 SIGWINCH 信号来更新屏幕大小，因此 VM 不会了解终端的大小。 安装可提供 `resize` 命令的 xterm 或类似实用工具，然后运行 `resize`。
无法粘贴长字符串。 | 串行控制台将粘贴到终端的字符串长度限制为 2048 个字符，以防止串行端口带宽过载。
SLES BYOS 映像中的键盘输入不正常。 仅限偶尔识别键盘输入。 | 这是 Plymouth 包的问题。 Plymouth 不应在 Azure 中运行，因为你不需要初始屏幕，Plymouth 会干扰平台使用串行控制台的功能。 删除 Plymouth `sudo zypper remove plymouth` ，然后重新启动。 或者，通过将追加 `plymouth.enable=0` 到行尾来修改 GRUB 配置的内核行。 可以通过以下方式执行此操作： [在启动时编辑启动项](./serial-console-grub-single-user-mode.md#single-user-mode-in-suse-sles)，或编辑中的 GRUB_CMDLINE_LINUX 行 `/etc/default/grub` ，重新生成 GRUB， `grub2-mkconfig -o /boot/grub2/grub.cfg` 然后重新启动。


## <a name="frequently-asked-questions"></a>常见问题

**问：如何发送反馈？**

A. 可以通过在 https://aka.ms/serialconsolefeedback 中创建 GitHub 问题来提供反馈。 也可以通过 azserialhelp@microsoft.com，或者 https://feedback.azure.com 上的虚拟机类别发送反馈（不太建议）。

**问：串行控制台是否支持复制/粘贴？**

A. 是的。 可以使用 **Ctrl**+**Shift**+**C** 和 **Ctrl**+**Shift**+**V** 复制并粘贴到终端。

**：.能否使用串行控制台而不是 SSH 连接？**

A. 虽然从技术上讲这种用法是可行的，但串行控制台主要用作无法通过 SSH 进行连接时的故障排除工具。 不建议使用串行控制台替代 SSH 的原因如下：

- 串行控制台的带宽不如 SSH 那样大。 由于它是仅限文本的连接，因此很难进行大量的 GUI 交互。
- 目前只能使用用户名和密码访问串行控制台。 由于 SSH 密钥比用户名/密码组合安全得多，因此，从登录安全性的立场，我们建议使用 SSH 而不是串行控制台。

**：.谁可以启用或禁用订阅的串行控制台？**

A. 若要在订阅范围级别启用或禁用串行控制台，必须拥有订阅的写入权限。 拥有写入权限的角色包括管理员或所有者角色。 自定义角色也可能具有写入权限。

**：.谁可以访问我的 VM/虚拟机规模集的串行控制台？**

A. 对于 VM 或虚拟机规模集，您必须具有虚拟机参与者角色或更高版本，才能访问串行控制台。

**问：我的串口控制台未显示任何内容，该怎么办？**

A. 你的映像可能配置错误，无法进行串行控制台访问。 有关配置映像以启用串行控制台的详细信息，请参阅[串行控制台 Linux 分发版可用性](#serial-console-linux-distribution-availability)。

**问：串行控制台是否可用于虚拟机规模集？**

A. 是的！ 请参阅[适用于虚拟机规模集的串行控制台](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**：.如果我只使用 SSH 密钥身份验证来设置我的 VM 或虚拟机规模集，我是否仍然可以使用串行控制台连接到我的 VM/虚拟机规模集实例？**

A. 是的。 由于串行控制台不需要 SSH 密钥，因此你只需设置用户名/密码组合。 为此，可以在 Azure 门户中选择“重置密码”，然后使用这些凭据登录到串行控制台。****

## <a name="next-steps"></a>后续步骤
* 使用串行控制台[访问 GRUB 和单用户模式](serial-console-grub-single-user-mode.md)。
* 使用串行控制台执行 [NMI 和 SysRq 调用](serial-console-nmi-sysrq.md)。
* 了解如何使用串行控制台 [在各种发行版中启用 GRUB](serial-console-grub-proactive-configuration.md)
* 串行控制台也适用于 [Windows vm](./serial-console-windows.md)。
* 详细了解[启动诊断](boot-diagnostics.md)。