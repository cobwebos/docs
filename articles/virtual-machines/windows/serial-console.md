---
title: Azure 虚拟机串行控制台 | Microsoft Docs
description: Azure 虚拟机的双向串行控制台。
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
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

## <a name="open-the-serial-console"></a>打开串行控制台
只能通过 [Azure 门户](https://portal.azure.com)访问虚拟机的串行控制台。 下面是通过门户访问虚拟机串行控制台的步骤 

  1. 打开 Azure 门户
  2. 在左侧菜单中选择虚拟机。
  3. 在列表中单击所需的 VM。 此时会打开该 VM 的概述页。
  4. 向下滚动到“支持 + 故障排除”部分，单击“串行控制台(预览版)”选项。 此时会打开一个包含串行控制台的新窗格，并启动连接。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>禁用功能
可以通过禁用特定 VM 的启动诊断设置，针对该 VM 停用串行控制台功能。

## <a name="serial-console-security"></a>串行控制台安全性 

### <a name="access-security"></a>访问安全性 
只有对虚拟机拥有 [VM 参与者](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)或更高访问权限的用户才能访问串行控制台。 如果 AAD 租户需要多重身份验证 (MFA)，则访问串行控制台时也需要执行 MFA，因为串行控制台是通过 [Azure 门户](https://portal.azure.com)访问的。

### <a name="channel-security"></a>通道安全性
通过网络来回发送的所有数据都经过加密。

### <a name="audit-logs"></a>审核日志
对串行控制台的所有访问目前都会记录在虚拟机的[启动诊断](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)日志中。 Azure 虚拟机管理员拥有并可控制这些日志的访问权限。  

>[!CAUTION] 
尽管不会记录控制台的访问密码，但如果在控制台中运行的命令包含或输出密码、机密、用户名或其他任何形式的个人身份信息 (PII)，则在实现串行控制台的回滚功能过程中，会将这些信息连同其他所有可见文本一起写入虚拟机的启动诊断日志。 这些日志不断轮替，只有对诊断存储帐户拥有读取权限的个人才能访问它们。但是，我们建议遵循有关将远程桌面用于涉及机密和/或 PII 的任何操作的最佳做法。 

### <a name="concurrent-usage"></a>并发使用
如果某个用户已连接到串行控制台，而另一个用户已成功请求访问同一个虚拟机，则第一个用户将断开连接，第二用户将建立连接，就好比是第一个用户站起来离开物理控制台，然后一个新用户在控制台旁边坐下来。

>[!CAUTION] 
这意味着，断开连接的用户并未注销！ 断开连接后强制注销（通过 SIGHUP 或类似机制）的功能目前仍在规划中。 对于 Windows，SAC 中会启用自动超时；但对于 Linux，可以配置终端超时设置。 


## <a name="accessing-serial-console-for-windows"></a>访问适用于 Windows 的串行控制台 
Azure 上较新的 Windows Server 映像默认情况下已启用[特殊管理控制台](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC)。 SAC 在服务器版本的 Windows 上受支持，但在客户端版本（例如 Windows 10、Windows 8 或 Windows 7）上不可用。 若要为使用 Feb2018 或更低版本的映像创建的 Windows 虚拟机启用串行控制台，请执行以下步骤： 

1. 通过远程桌面连接到 Windows 虚拟机
2. 从管理命令提示符运行以下命令 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. 重新启动系统以启用 SAC 控制台

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

如果需要，也可以使 SAC 脱机。 

1. 将想要为其配置 SAC 的 Windows 磁盘作为数据磁盘附加到现有 VM。 
2. 从管理命令提示符运行以下命令 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>如何知道是否已启用 SAC 

如果未启用 [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)，串行控制台将不会显示 SAC 提示符。 在某些情况下它可能会显示 VM 运行状况信息，或者会为空。  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>让启动菜单显示在串行控制台中 

如果需要让 Windows 启动加载程序提示符显示在串行控制台中，可以将以下附加选项添加到 Windows 启动加载程序。

1. 通过远程桌面连接到 Windows 虚拟机
2. 从管理命令提示符运行以下命令 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. 重新启动系统以启用启动菜单

> [!NOTE] 
> 就目前而言，对功能键的支持未启用，如果需要高级启动选项，请使用 use bcdedit /set {current} onetimeadvancedoptions on，有关详细信息，请参阅 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>访问 Windows 串行控制台的常见场景 
场景          | 串行控制台中的操作                
:------------------|:----------------------------------------
错误的防火墙规则 | 访问串行控制台，并修复 iptables 或 Windows 防火墙规则 
文件系统损坏/检查 | 访问串行控制台并在登录到 SAC CMD 后恢复文件系统
RDP 配置问题 | 访问串行控制台并登录到 cmd 通道。 检查终端服务运行状况，并根据需要重启。
网络锁定系统| 访问串行控制台并登录到 cmd 通道。 通过 [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts) 命令行检查防火墙状态。 

## <a name="errors"></a>Errors
大多数错误都是暂时性的，重试连接即可解决。 下表显示了错误和缓解措施的列表 

错误                            |   缓解措施 
:---------------------------------|:--------------------------------------------|
无法检索“<VMNAME>”的启动诊断设置。 若要使用串行控制台，请确保为此 VM 启用了启动诊断。 | 确保 VM 已启用[启动诊断](boot-diagnostics.md)。 
VM 处于已停止/已解除分配状态。 启动 VM，然后重试串行控制台连接。 | 虚拟机必须处于已启动状态才能访问串行控制台
没有所需的权限，无法使用此 VM 的串行控制台。 请确保至少拥有 VM 参与者角色权限。| 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
无法确定启动诊断存储帐户“<STORAGEACCOUNTNAME>”的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)

## <a name="known-issues"></a>已知问题 
由于串行控制台访问功能目前仍处于预览阶段，我们正在努力解决一些已知问题。下面是已知问题及其可能的解决方法的列表 

问题                           |   缓解措施 
:---------------------------------|:--------------------------------------------|
没有相应的选项用于访问虚拟机规模集实例的串行控制台 | 在预览期，不支持访问虚拟机规模集实例的串行控制台。
在出现连接标题后按 Enter 不会显示登录提示 | [按 Enter 不起任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
连接到 Windows VM 时，仅显示运行状况信息| [Windows 运行状况信号](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>常见问题 
**问：如何发送反馈？**

A. 可以访问 https://aka.ms/serialconsolefeedback 来提供问题反馈。 也可以通过 azserialhelp@microsoft.com，或者 http://feedback.azure.com 上的虚拟机类别发送反馈（不太建议） 

**问：我遇到了错误“现有控制台的 OS 类型‘Windows’与请求的 OS 类型‘Linux’相冲突”，为什么？**

A. 这是一个已知问题，若要解决此问题，只需在 bash 模式下打开 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)，然后重试。

**问：我无法访问串行控制台，在哪里可以提出支持案例？**

A. 此预览功能遵守 Azure 预览条款。 最好是通过上面所述的渠道请求此功能的支持。 

## <a name="next-steps"></a>后续步骤
* 串行控制台也适用于 [Linux](../linux/serial-console.md) VM
* 详细了解[启动诊断](boot-diagnostics.md)
