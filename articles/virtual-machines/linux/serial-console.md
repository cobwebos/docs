---
title: Azure 虚拟机串行控制台 | Microsoft Docs
description: Azure 虚拟机的双向串行控制台。
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: b7d6e48a6f34472bc38947fd70e850b1c3bf6f8a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>虚拟机串行控制台（预览版） 


使用 Azure 上的虚拟机串行控制台可以访问适用于 Linux 和 Windows 虚拟机的基于文本的控制台。 此控制台与虚拟机的 COM1 串行端口建立串行连接，可用于访问虚拟机，且不与虚拟机的网络/操作系统状态相关。 目前，只能通过 Azure 门户访问虚拟机的串行控制台，并且只允许对虚拟机拥有 VM 参与者或更高访问权限的用户执行此操作。 

> [!Note] 
> 同意使用条款即可使用预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 目前，此服务以**公共预览版**提供，可以访问已在 Azure 全球区域开通的虚拟机的串行控制台。 目前，串行控制台不可用于 Azure 政府云、Azure 德国云和 Azure 中国云。


## <a name="prerequisites"></a>先决条件 

* 虚拟机上必须已启用[启动诊断](boot-diagnostics.md) 
* 使用串行控制台的帐户必须对 VM 和[启动诊断](boot-diagnostics.md)存储帐户拥有[参与者角色](../../active-directory/role-based-access-built-in-roles.md)。 
* 有关特定于 Linux 分发版的设置，请参阅[访问适用于 Linux 的串行控制台](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>打开串行控制台
只能通过 [Azure 门户](https://portal.azure.com)访问虚拟机的串行控制台。 下面是通过门户访问虚拟机串行控制台的步骤 

  1. 打开 Azure 门户
  2. 在左侧菜单中选择虚拟机。
  3. 在列表中单击所需的 VM。 此时会打开该 VM 的概述页。
  4. 向下滚动到“支持 + 故障排除”部分，单击“串行控制台(预览版)”选项。 此时会打开一个包含串行控制台的新窗格，并启动连接。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> 串行控制台要求配置一个带有密码的本地用户。 目前，仅仅配置有 SSH 公钥的 VM 无法访问串行控制台。 若要创建带有密码的本地用户，请遵循 [VM 访问扩展](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)并创建带有密码的本地用户。

### <a name="disable-feature"></a>禁用功能
可以通过禁用特定 VM 的启动诊断设置，针对该 VM 停用串行控制台功能。

## <a name="serial-console-security"></a>串行控制台安全性 

### <a name="access-security"></a>访问安全性 
只有对虚拟机拥有 [VM 参与者](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)或更高访问权限的用户才能访问串行控制台。 如果 AAD 租户需要多重身份验证 (MFA)，则访问串行控制台时也需要执行 MFA，因为串行控制台是通过 [Azure 门户](https://portal.azure.com)访问的。

### <a name="channel-security"></a>通道安全性
通过网络来回发送的所有数据经过加密。

### <a name="audit-logs"></a>审核日志
对串行控制台的所有访问目前都会记录在虚拟机的[启动诊断](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)日志中。 Azure 虚拟机管理员拥有并可控制这些日志的访问权限。  

>[!CAUTION] 
尽管不会记录控制台的访问密码，但如果在控制台中运行的命令包含或输出密码、机密、用户名或其他任何形式的个人身份信息 (PII)，则在实现串行控制台的回滚功能过程中，会将这些信息连同其他所有可见文本一起写入虚拟机的启动诊断日志。 这些日志不断轮替，只有对诊断存储帐户拥有读取权限的个人才能访问它们。但是，我们建议遵循有关将 SSH 控制台用于涉及机密和/或 PII 的任何操作的最佳做法。 

### <a name="concurrent-usage"></a>并发使用
如果某个用户已连接到串行控制台，而另一个用户已成功请求访问同一个虚拟机，则第一个用户将断开连接，第二用户将建立连接，就好比是第一个用户站起并离开物理控制台，然后一个新用户坐在控制台旁边。

>[!CAUTION] 
这意味着，断开连接的用户并未注销！ 断开连接后强制注销（通过 SIGHUP 或类似机制）的功能目前仍在规划中。 对于 Windows，SAC 中会启用自动超时；但对于 Linux，可以配置终端超时设置。 为此，只需将 `export TMOUT=600` 添加到登录控制台时所用的用户帐户的 .bash_profile 或 .profile 中，以便在 10 分钟后使会话超时。

### <a name="disable-feature"></a>禁用功能
可以通过禁用特定 VM 的启动诊断设置，针对该 VM 停用串行控制台功能。

## <a name="common-scenarios-for-accessing-serial-console"></a>访问串行控制台的常见场景 
场景          | 串行控制台中的操作                |  OS 适用性 
:------------------|:-----------------------------------------|:------------------
受损的 FSTAB 文件 | 按 Enter 键继续，然后使用文本编辑器修复 /etc/fstab 文件。 请参阅[如何修复 fstab 问题](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
错误的防火墙规则 | 访问串行控制台，并修复 iptables 或 Windows 防火墙规则 | Linux/Windows 
文件系统损坏/检查 | 访问串行控制台并恢复文件系统 | Linux/Windows 
SSH/RDP 配置问题 | 访问串行控制台并更改设置 | Linux/Windows 
网络锁定系统| 通过门户访问串行控制台以管理系统 | Linux/Windows 
与引导加载程序交互 | 通过串行控制台访问 GRUB/BCD | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>访问适用于 Linux 的串行控制台
要使串行控制台正常运行，必须将来宾操作系统配置为向串行端口读取和写入控制台消息。 大多数[认可的 Azure Linux 分发版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)默认都已配置串行控制台。 只需在门户中的“串行控制台”部分单击一下，即可访问控制台。 

### <a name="access-for-redhat"></a>在 RedHat 中访问 
Azure 中提供的 RedHat 映像默认已启用控制台访问。 RedHat 中的单用户模式需要启用 root 用户（默认已禁用）。 如果需要启用单用户模式，请遵照以下说明：

1. 通过 SSH 登录到 RedHat 系统
2. 启用 root 用户的密码 
 * `passwd root`（设置强 root 密码）
3. 确保 root 用户只能通过 ttyS0 登录
 * 运行 `edit /etc/ssh/sshd_config` 并确保 PermitRootLog in 设置为 no
 * 运行 `edit /etc/securetty file`，以便只允许通过 ttyS0 登录 

现在，如果系统启动到单用户模式，则你可以通过 root 密码登录。

对于 RHEL 7.4+ 或 6.9+ 版本，可以在 GRUB 提示符下启用单用户模式，请参阅的[此处](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)的说明

### <a name="access-for-ubuntu"></a>在 Ubuntu 中访问 
Azure 中提供的 Ubuntu 映像默认已启用控制台访问。 如果系统启动到单用户模式，则你无需提供其他凭据即可访问。 

### <a name="access-for-coreos"></a>在 CoreOS 中访问
Azure 中提供的 CoreOS 映像默认已启用控制台访问。 如果需要，可以通过更改 GRUB 参数将系统启动到单用户模式；添加 `coreos.autologin=ttyS0` 可让核心用户登录并使用串行控制台。 

### <a name="access-for-suse"></a>在 SUSE 中访问
Azure 中提供的 SLES 映像默认已启用控制台访问。 如果使用 Azure 中的旧版 SLES，请遵照此[知识库文章](https://www.novell.com/support/kb/doc.php?id=3456486)启用串行控制台。 如果系统已启动到紧急模式，则 SLES 12 SP3+ 的较新映像还允许通过串行控制台访问。

### <a name="access-for-centos"></a>在 CentOS 中访问
Azure 中提供的 CentOS 映像默认已启用控制台访问。 对于单用户模式，请遵照类似于上述适用于 RedHat 映像的说明。 

### <a name="access-for-oracle-linux"></a>在 Oracle Linux 中访问
Azure 中提供的 Oracle Linux 映像默认已启用控制台访问。 对于单用户模式，请遵照类似于上述适用于 RedHat 映像的说明。

### <a name="access-for-custom-linux-image"></a>在自定义 Linux 映像中访问
若要为自定义 Linux VM 映像启用串行控制台，请在 /etc/inittab 中启用控制台访问，以便在 ttyS0 上运行终端。 下面是在 inittab 文件中添加此条目的示例 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Errors
大多数错误都是暂时性的，重试连接即可解决。 下表显示了错误和缓解措施的列表 

错误                            |   缓解措施 
:---------------------------------|:--------------------------------------------|
无法检索“<VMNAME>”的启动诊断设置。 若要使用串行控制台，请确保为此 VM 启用了启动诊断。 | 确保 VM 已启用[启动诊断](boot-diagnostics.md)。 
VM 处于已停止/已解除分配状态。 启动 VM，然后重试串行控制台连接。 | 虚拟机必须处于已启动状态才能访问串行控制台
没有所需的权限，无法使用此 VM 来访问串行控制台。 请确保至少拥有 VM 参与者角色权限。| 需有特定的权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
无法确定启动诊断存储帐户“<STORAGEACCOUNTNAME>”的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | 需有特定的权限才能访问串行控制台。有关详细信息，请参阅[访问要求](#prerequisites)

## <a name="known-issues"></a>已知问题 
由于串行控制台访问功能目前仍处于预览阶段，我们正在努力解决一些已知问题。下面是已知问题及其可能的解决方法的列表 

问题                           |   缓解措施 
:---------------------------------|:--------------------------------------------|
没有相应的选项用于访问虚拟机规模集实例的串行控制台 |  在预览期，不支持访问虚拟机规模集实例的串行控制台。
在出现连接标题后按 Enter 不会显示登录提示 | [按 Enter 不起任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>常见问题 
**问：如何发送反馈？**

A. 可以访问 https://aka.ms/serialconsolefeedback 来提供问题反馈。 也可以通过 azserialhelp@microsoft.com，或者 http://feedback.azure.com 上的虚拟机类别发送反馈（不太建议）

**问：我遇到了错误“现有控制台的 OS 类型‘Windows’与请求的 OS 类型‘Linux’相冲突”，为什么？**

A. 这是一个已知问题，若要解决此问题，只需在 bash 模式下打开 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)，然后重试。

**问：我无法访问串行控制台，在哪里可以发起支持案例？**

A. 此预览功能遵守 Azure 预览条款。 最好是通过上面所述的渠道请求此功能的支持。 

## <a name="next-steps"></a>后续步骤
* 串行控制台也适用于 [Windows](../windows/serial-console.md) VM。
* 详细了解[启动诊断](boot-diagnostics.md)