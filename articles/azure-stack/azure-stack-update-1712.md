---
title: Azure Stack 1712 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统 1712 版的新增功能、已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: brenduns
ms.openlocfilehash: c3cb8ab8a838a3f831ece617fcf3e218a9510ea5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1712-update"></a>Azure Stack 1712 更新

*适用于：Azure Stack 集成系统*

本文介绍此更新包中的改进与修复、此版本的已知问题，以及更新的下载位置。 已知问题分为直接与更新过程相关的已知问题，以及内部版本（安装后）相关的已知问题。

> [!IMPORTANT]
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1712 更新内部版本号为 **180106.1**。 如果客户以前已部署 **180103.2**，则无需应用 **180106.1**。

## <a name="before-you-begin"></a>开始之前

> [!IMPORTANT]
> 在安装 1712 更新过程中，请勿尝试创建虚拟机。 有关详细信息，请参阅[有关在 Azure Stack 中管理更新的概述](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates)。

### <a name="prerequisites"></a>必备组件

在应用此更新之前，必须先安装 Azure Stack [1711 更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711)。

### <a name="post-update-steps"></a>更新后的步骤

此更新还要求在完成 1712 Azure Stack 更新安装之后安装 OEM 合作伙伴提供的固件更新。

> [!NOTE]
> 请参阅 OEM 合作伙伴网站来下载更新。

### <a name="new-features-and-fixes"></a>新功能和修复

此更新包含以下适用于 Azure Stack 的改进和修复。

#### <a name="new-features"></a>新增功能

- 可以通过特权终结点获使用用于验证 Azure Stack 云的 Test-AzureStack cmdlet
- 可以注册已断开连接的 Azure Stack 部署
- 有关证书和用户帐户过期的监视警报
- 在 PEP 中添加了 Set-BmcPassword cmdlet 用于轮换 BMC 密码
- 网络日志记录更新，支持按需日志记录
- 支持虚拟机规模集 (VMSS) 的重置映像操作
- 支持在 ERCS VM 上使用展台模式进行 CloudAdmin 登录
- 租户可以自动激活 Windows VM

#### <a name="fixes"></a>修复项

- 现在，在运行修复时会将节点工作状态显示为维护中
- 现在，会显示正确的公共 IP 用量记录日期/时间戳
- 其他多项性能、稳定性和安全修复
- TimeSource 和 Defender 特权终结点模块 bug 修复

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 的新功能和修复

- [2018 年 1 月 3 日 — KB4056890（OS 内部版本 14393.2007）](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - 此更新包含 [MSRC 安全公告 ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) 所述的行业级安全问题的软件修复。

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

本部分描述在安装 1712 更新期间可能会遇到的已知问题。

1. **症状：**Azure Stack 操作员在更新过程中可能会看到以下错误：*“角色 'VirtualMachines' 的类型 'CheckHealth' 引发了异常:\n\n-ACS01 的虚拟机运行状况检查生成了以下错误。\n从主机获取 VM 信息时出错。异常详细信息:\nGet-VM: 计算机 'Node03' 上的操作失败: WS-Management 服务无法处理请求。WMI \nservice 或 WMI 提供程序返回了未知的错误: HRESULT 0x8004106c”。*
    1. **原因：**此问题是由某个 Windows Server 问题造成的，后续的 Windows Server 更新中会解决此问题。
    2. **解决方法：**请联系 Microsoft 客户服务和支持 (CSS) 寻求帮助。
<br><br>
2. **症状：**Azure Stack 操作员在更新过程中可能会看到以下错误：“在 Host-Node03 节点上启用种子环 VM 失败，并出现以下错误: [Host-Node03] 连接到远程服务器 Host-Node03 失败，并出现以下错误消息: WinRM 客户端收到 HTTP 服务器错误状态 (500)，但远程服务并不包含有关失败原因的其他任何信息。”
    1. **原因：**此问题是由某个 Windows Server 问题造成的，后续的 Windows Server 更新中会解决此问题。 
    2. **解决方法：**请联系 Microsoft 客户服务和支持 (CSS) 寻求帮助。
<br><br>

### <a name="known-issues-post-installation"></a>已知问题（安装后）

本部分包含内部版本 **180106.1** 的安装后已知问题。

#### <a name="portal"></a>门户

- 无法在管理员门户中查看计算或存储资源。 这表示更新安装期间出错，并误将更新报告为成功。 如果发生此问题，请联系 Microsoft CSS 寻求帮助。
- 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。
- 查看资源组的属性时，“移动”按钮已禁用。 这是预期的行为。 目前不支持在订阅之间移动资源组。
- 对于可在下拉列表中选择订阅、资源组或位置的任何工作流，可能会遇到一个或多个以下问题：

   - 可能会在列表顶部看到一个空白行。 应该仍能按预期选择项。
   - 如果下拉列表中的项列表很短，可能无法查看任何项名称。
   - 如果有多个用户订阅，资源组的下拉列表可能是空的。

        > [!NOTE]
        > 若要解决后两个问题，可以输入订阅或资源组的名称（如果知道），或者可以改用 PowerShell。

- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。
- **服务运行状况**边栏选项卡无法加载。 当你打开的服务运行状况边栏选项卡在管理员或用户门户中，Azure 堆栈会显示错误并不会加载信息。 这是预期的行为。 尽管你可以选择并打开服务运行状况，但此功能尚不可用，但将实现 Azure 堆栈的未来版本中。

#### <a name="health-and-monitoring"></a>运行状况和监视

- 如果重新启动基础结构角色实例，可能会收到一条消息，指出重新启动失败。 但实际上重新启动已成功。

#### <a name="marketplace"></a>Marketplace
- 出于兼容性考虑，此版本中将会删除一些 Marketplace 项。 在进一步验证后，会重新启用这些项。
- 用户无需订阅就能浏览整个 Marketplace，并且将会看到计划和产品/服务等管理项。 对用户而言，这些项是非功能性的。

#### <a name="compute"></a>计算
- 用户可以使用相应的选项创建包含异地冗余存储的虚拟机。 此配置会导致虚拟机创建失败。
- 可以配置只包含一个容错域和一个更新域的虚拟机可用性集。
- 没有任何可用于创建虚拟机规模集的 Marketplace 体验。 可以使用模板来创建规模集。
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

#### <a name="networking"></a>网络
- 无法使用门户创建采用公共 IP 地址的负载均衡器。 解决方法是使用 PowerShell 创建负载均衡器。
- 创建网络负载均衡器时，必须创建网络地址转换 (NAT) 规则。 否则，在创建负载均衡器之后尝试添加 NAT 规则时会收到错误。
- 创建 VM 并与公共 IP 地址创建关联之后，无法取消该 IP 地址与虚拟机 (VM) 的关联。 取消关联看似正常运行，但以前分配的公共 IP 地址仍与原始 VM 相关联。 即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。 目前只有在创建新的 VM 时，才能使用新的公共 IP 地址。
- Azure Stack 操作员可能无法部署、删除、修改 VNET 或网络安全组。 此问题主要出现于同一个包的后续更新尝试。 此问题的原因是发生了目前正在调查的更新打包问题。
- 内部负载均衡 (ILB) 对 MAC 地址的后端 VM 进行不恰当的处理，导致 Linux 实例损坏。

#### <a name="sqlmysql"></a>SQL/MySQL
- 最长可能需要在一小时后，租户才能在新的 SQL 或 MySQL SKU 中创建数据库。
- 不支持在 SQL 和 MySQL 托管服务器中直接创建不是由资源提供程序执行的项，这可能导致不匹配的状态。

    > [!NOTE]
    > 将 Azure Stack 集成系统更新到 1712 版时，不应影响现有的 SQL 或 MySQL 资源提供程序用户。 在新的 Azure Stack 更新发布之前，可以继续使用当前的 SQL 或 MySQL 资源提供程序内部版本。

#### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

#### <a name="identity"></a>标识

在已部署 Azure Active Directory 联合身份身份验证服务 (ADFS) 的环境中，**azurestack\azurestackadmin** 帐户不再是默认提供商订阅的所有者。 如果不使用 **azurestack\azurestackadmin** 登录到**管理门户/adminmanagement 终结点**，可以使用 **azurestack\cloudadmin** 帐户，以便可以管理和使用默认提供商订阅。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帐户是 ADFS 部署环境中默认提供商订阅的所有者，它也没有通过 RDP 连接到主机的权限。 根据需要继续使用 **azurestack\azurestackadmin** 帐户或本地管理员帐户来登录、访问和管理主机。

## <a name="download-the-update"></a>下载更新

可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1712 更新包。

## <a name="more-information"></a>详细信息

Microsoft 已提供某种方式让用户使用装有 1712 更新的特权终结点 (PEP) 来监视和恢复更新。

- 请参阅文档：[使用特权终结点监视 Azure Stack 中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)。 

## <a name="see-also"></a>另请参阅

- 有关 Azure Stack 中的更新管理的概述，请参阅[有关在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
