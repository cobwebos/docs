---
title: "Azure 堆栈 1712年更新 |Microsoft 文档"
description: "了解什么是 Azure 堆栈 1712年更新中集成的系统、 已知的问题和下载更新的位置。"
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: andredm
ms.openlocfilehash: 92708909a472f98b45492f3c0c807634f1c204d7
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="azure-stack-1712-update"></a>Azure 堆栈 1712年更新

*适用范围： Azure 堆栈集成系统*

本文介绍了改进，并修复的已知问题有关此版本中，以及在何处下载更新此更新包中。 已知问题分为已知问题直接相关的更新过程中，并生成 （安装后） 的已知问题。

> [!IMPORTANT]
> 此更新包仅适用于 Azure 堆栈集成系统。 不适用于此更新包 Azure 堆栈开发工具包。

## <a name="build-reference"></a>生成参考

Azure 堆栈 1712年更新生成号是**180103.2**。

## <a name="before-you-begin"></a>开始之前

> [!WARNING]
> 如果你当前的活动的交互 PEP 远程会话中，你需要退出初始化 Azure 堆栈更新安装过程之前会话。 你可以键入**退出**远程控制台退出会话中。
> 你可以检查是否有任何现有的 PEP 远程会话通过运行两个 PowerShell 命令，首先**Get-pssession**其中列出了所有活动的远程会话，然后**Remove-pssession**终止和删除活动的远程会话。 PowerShell 命令需要的任何用于在 Azure 堆栈环境中创建 PEP 远程会话的计算机上运行。 下面是 PowerShell 命令示例：
```
Get-PSSession | Remove-PSSession
```
此外，不要创建 PEP 会话甚至 Azure 堆栈更新已启动后。

> [!IMPORTANT]
> 请勿尝试来 1712年更新安装过程中创建虚拟机。 请参阅[管理 Azure 堆栈概述中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates)有关详细信息。

### <a name="prerequisites"></a>必备组件

你必须首先安装 Azure 堆栈[1711年更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711)应用此更新之前。

### <a name="post-update-steps"></a>更新后步骤

此更新还要求你安装固件更新从 OEM 合作伙伴 1712 Azure 堆栈更新安装完成后。

> [!NOTE]
> 请参阅你的 OEM 合作伙伴网站，以下载更新。

### <a name="new-features-and-fixes"></a>新功能和修补程序

此更新包括以下改进和修复 Azure 堆栈。

#### <a name="new-features"></a>新增功能

- 新用户体验时从应用商店创建项
- 测试 AzureStack cmdlet 来验证 Azure 堆栈云可通过特权终结点
- 注册 Azure 堆栈的断开连接的部署能力
- 证书和用户帐户到期的监视警报
- 添加在 PEP BMC 密码循环的更新 BMCCredential cmdlet
- 网络日志记录更新，以支持按需日志记录
- 支持重置映像操作的虚拟机规模集 (VMSS)
- 启用 ERCS VM 上 CloudAdmin 登录名的展台模式
- 租户可以自动激活 Windows Vm

#### <a name="fixes"></a>修复项

- 用于运行修复时显示处于维护状态的节点的操作状态的修补程序
- 固定到正确的公共 IP 使用情况记录时间/日期戳
- 各种其他性能、 稳定性和安全修补程序
- 时间源和 Defender 特权终结点模块 bug 修复

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 的新功能和修补程序

- [年 1 月，第三-2018年-KB4056890 （OS 内部 14393.2007）](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - 此更新包括行业范围的安全问题所描述的软件修补[MSRC 安全公告 ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)。
 
### <a name="known-issues-with-the-update-process"></a>在更新过程的已知的问题

本部分包含 1712年更新安装过程中可能遇到的已知的问题。

1. **症状：** Azure 堆栈运算符在更新过程中可能会看到以下错误： *"角色 VirtualMachines 的类型 CheckHealth 引发异常： \n\nVirtual 计算机运行状况检查的-ACS01 产生以下错误。\nThere 时从主机中获取 VM 信息时出错。异常详细信息： \nGet-VM: Node03 失败的计算机上的操作： Ws-management 服务无法处理该请求。WMI \nservice 或 WMI 提供程序返回了一个未知的错误： HRESULT 0x8004106c。"*
    1. **原因：**由 Windows Server 问题，用于在后续窗口服务器更新中得以解决导致此问题。
    2. **解决方法：**联系 Microsoft 客户服务和支持 (CSS) 以获取帮助。
<br><br>
2. **症状：** Azure 堆栈运算符在更新过程中可能会看到以下错误：*"启用 VM 失败并出错节点主机 Node03 种子环: [主机 Node03] 连接到远程服务器主机 Node03 失败，出现以下错误消息： WinRM 客户端收到 HTTP 服务器错误状态 (500)，但远程服务未不包括任何其他原因的信息失败。"*
    1. **原因：**由 Windows Server 问题，用于在后续窗口服务器更新中得以解决导致此问题。 
    2. **解决方法：**联系 Microsoft 客户服务和支持 (CSS) 以获取帮助。
<br><br>

### <a name="known-issues-post-installation"></a>已知的问题 （安装后）

本部分包含安装后生成的已知的问题**180103.2**。

#### <a name="portal"></a>门户

- 它不能在管理员门户中查看计算或存储资源。 这表示安装更新期间出现错误，更新已正确地报告为成功。 如果出现此问题，请与 Microsoft CSS 联系以获取帮助。
- 你可能会看到在门户中的空白仪表板。 若要恢复仪表板，门户中，右上角选择齿轮图标，然后选择**还原默认设置**。
- 在查看资源组的属性时**移动**按钮处于禁用状态。 此行为被预期行为。 当前不支持订阅之间移动资源组。
- 对于任何工作流，其中在下拉列表中选择订阅、 资源组或位置，你可能会遇到一个或多个以下问题：

   - 你可能会看到在列表顶部的空行。 你仍应将能够按预期方式选择一个项。
   - 如果下拉列表中项的列表很短，可能无法查看任何项名称。
   - 如果你有多个用户订阅，资源组下拉列表可能为空。 

        > [!NOTE]
        > 若要解决的最后两个问题，你可键入名称的订阅或资源组 （如果你知道它），也可以改为使用 PowerShell。

- 删除孤立资源导致的用户订阅。 解决方法是，首先删除用户资源或整个资源组中，，然后再删除用户订阅。
- 你不能查看你的订阅使用 Azure 堆栈门户的权限。 一种解决方法，可以通过使用 PowerShell 来验证权限。

#### <a name="health-and-monitoring"></a>运行状况和监视

- 如果重新启动基础结构角色实例，你可能会收到一条消息指出在重新启动失败。 但是，在重新启动实际成功。

#### <a name="marketplace"></a>Marketplace
- 要在此版本中由于兼容性问题删除一些应用商店项。 在进一步验证之后，这些将是重新启用。
- 用户可以浏览没有订阅，但完整的应用商店，并可以看到等计划，并提供管理项目。 这些项是向用户无法正常工作。

#### <a name="compute"></a>计算
- 为用户提供的选项以使用地域冗余存储创建虚拟机。 此配置导致虚拟机创建失败。
- 你可以配置虚拟机的可用性仅使用容错域之一，以及一个更新域设置。
- 没有任何应用商店体验，以创建虚拟机规模集。 你可以创建缩放集使用的模板。
- 缩放设置的虚拟机规模集不是在门户中提供的。 作为一种解决方法，你可以使用[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，你必须使用`-Name`参数而不是`-VMScaleSetName`。
 
#### <a name="networking"></a>网络
- 使用门户，不能具有公共 IP 地址创建负载平衡器。 一种解决方法，你可以使用 PowerShell 创建负载平衡器。
- 创建一个网络负载平衡时，必须创建网络地址转换 (NAT) 规则。 如果没有，你将收到错误，当你尝试创建负载平衡器后添加的 NAT 规则。
- 创建 VM 并将其与该 IP 地址相关联后，无法解除虚拟机 (VM) 中的公共 IP 地址的关联。 解除关联看起来工作，但之前分配的公共 IP 地址保留与原始 VM 相关联。 如果即使重新分配到新的 VM 的 IP 地址，则会发生此行为 (通常称为*VIP 交换*)。 所有未来都尝试连接通过在连接中，于最初关联的 VM，而不适用于新一个此 IP 地址结果。 目前，仅必须使用用于新 VM 创建的新公共 IP 地址。
- Azure 堆栈运算符可能不能部署、 删除、 修改 Vnet 或网络安全组。 在同一个包的后续更新尝试主要出现此问题。 这被引起更新其中我们正在调查打包问题。
- 内部负载平衡 (ILB) 不正确处理的中断 Linux 实例后端 Vm 的 MAC 地址。
 
#### <a name="sqlmysql"></a>SQL/MySQL
- 它可能需要一小时之前租户可以在新的 SQL 或 MySQL SKU 中创建数据库。 
- 直接在 SQL 和 MySQL 宿主服务器不由资源提供程序执行上的项目创建不支持，并且可能会导致不匹配的状态。

    > [!NOTE]
    > 更新你的 Azure 堆栈集成系统到 1712年版本时，你应不会向现有 SQL 或 MySQL 资源提供程序用户影响。 你可以继续使用当前 SQL 或 MySQL 资源提供程序生成，直到有可用的新的 Azure 堆栈更新。

#### <a name="app-service"></a>应用服务
- 在订阅中创建其第一个 Azure 函数之前，用户必须注册存储资源提供程序。

#### <a name="identity"></a>标识

在 Azure Active Directory 联合身份验证服务 (ADFS) 已部署环境， **azurestack\azurestackadmin**帐户不再是默认提供程序订阅的所有者。 而不是登录到**管理门户 / adminmanagement 终结点**与**azurestack\azurestackadmin**，你可以使用**azurestack\cloudadmin**帐户，因此你可以管理并使用默认提供程序订阅。

> [!IMPORTANT]
> 即使**azurestack\cloudadmin**帐户是在 ADFS 部署环境中的默认提供程序订阅的所有者，它没有到 RDP 到主机的权限。 继续使用**azurestack\azurestackadmin**帐户或本地管理员帐户才能登录、 访问和管理主机，根据需要。

## <a name="download-the-update"></a>下载更新

你可以下载 Azure 堆栈 1712年更新包从[此处](https://aka.ms/azurestackupdatedownload)。

## <a name="see-also"></a>另请参阅

- 请参阅[管理 Azure 堆栈概述中的更新](azure-stack-updates.md)有关 Azure 堆栈中的更新管理的概述。
- 请参阅[在 Azure 堆栈中应用更新](azure-stack-apply-updates.md)有关如何使用 Azure 堆栈应用更新的详细信息。
