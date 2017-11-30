---
title: "Azure 堆栈 1711年更新 |Microsoft 文档"
description: "了解什么是 Azure 堆栈 1711年更新中集成的系统、 已知的问题和下载更新的位置。"
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: andredm
ms.openlocfilehash: 4d98556f17fa834c497c2d1cd1854c9e6b02e021
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2017
---
# <a name="azure-stack-1711-update"></a>Azure 堆栈 1711年更新

*适用范围： Azure 堆栈集成系统*

本文介绍了改进，并修复的已知问题有关此版本中，以及在何处下载更新此更新包中。 已知问题分为已知问题直接相关的更新过程中，并生成 （安装后） 的已知问题。

> [!IMPORTANT]
> 此更新包仅适用于 Azure 堆栈集成系统。 不适用于此更新包 Azure 堆栈开发工具包。

## <a name="build-reference"></a>生成参考

Azure 堆栈 1711年更新生成号是**20171122.1**。

## <a name="before-you-begin"></a>开始之前

### <a name="prerequisites"></a>必备组件

你必须首先安装 Azure 堆栈[1710年更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710)应用此更新之前。

### <a name="new-features-and-fixes"></a>新功能和修补程序

此更新包括以下改进和修复 Azure 堆栈。

#### <a name="new-features"></a>新增功能

- 对联合的解决方案模板的支持
- 在 Azure 堆栈 Graph 日志记录和错误处理中的更新
- 打开主机打开和关闭功能
- 用户可以自动激活 Windows Vm
- 添加的特权终结点 PowerShell cmdlet 来检索保留目的的 BitLocker 恢复密钥
- 支持更新基础结构时，更新脱机映像

#### <a name="fixes"></a>修复项

- 在现场可更换部件 (FRU) 和日志记录还更新的群集期间，在 DNS 中的固定的争用条件
- 对的现场可更换部件 (FRU) 中的禁用主机的重新启动功能进行了修复
- 各种其他性能、 安全和稳定性修补程序

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 的新功能和修补程序

- [2017 年 11 月 14 日-KB4048953 （OS 内部 14393.1884）](https://support.microsoft.com/help/4048953)
 
### <a name="known-issues-with-the-update-process"></a>在更新过程的已知的问题

本部分包含 1711年更新安装过程中可能遇到的已知的问题。


1. **症状：** Azure 堆栈运算符在更新过程中可能会看到以下错误： *"名称： 安装更新。"，"说明":"在主机和基础 Vm 上的安装更新，""errorMessage":"键入 LiveUpdate 的角色VirtualMachines 的引发异常： \n\nThere disk.\n\nat 上空间不足<ScriptBlock>， <No file>: line22"，"状态":"错误"、"startTimeUtc":"自 2017 年 1-11-10T16:46:59.123Z"，"endTimeUtc":"自 2017 年 1-11-10T19:20:29.669Z"，"步骤": []"*
    2. **原因：**此问题由属于 Azure 堆栈基础结构的一个或多个虚拟机上的可用磁盘空间不足
    3. **解决方法：**联系 Microsoft 客户服务和支持 (CSS) 以获取帮助。
<br><br>
2. **症状：** Azure 堆栈运算符在更新过程中可能会看到以下错误：*带"3"的参数调用"ExtractToFile"的异常:"进程无法访问文件 <\\<machineName>-ERCS01\C$ \程序 Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll >*
    1. **原因：**恢复来自以前的门户的更新恢复使用特权终结点 (PEP) 时，会出现此问题。
    2. **解决方法：**联系 Microsoft 客户服务和支持 (CSS) 以获取帮助。
<br><br>
3. **症状：**Azure 堆栈运算符在更新过程中可能会看到以下错误：*"角色 VirtualMachines 的类型 CheckHealth 引发的异常： \n\nVirtual 机运行状况检查<machineName>-ACS01 生成以下错误。 \nThere 时从主机中获取 VM 信息时出错。异常详细信息： \nGet-VM: Node03 失败的计算机上的操作： Ws-management 服务无法处理该请求。WMI \nservice 或 WMI 提供程序返回了一个未知的错误： HRESULT 0x8004106c"。*
    1. **原因：**由 Windows Server 问题，用于在后续窗口服务器更新中得以解决导致此问题。
    2. **解决方法：**联系 Microsoft 客户服务和支持 (CSS) 以获取帮助。
<br><br>
4. **症状：**Azure 堆栈运算符在更新过程中可能会看到以下错误：*"角色 URP 的类型 DefenderUpdate 引发了异常： 失败获取从版本\\SU1FileServer\SU1_Public\DefenderUpdates\x64\{文件名} 在复制 AzSDefenderFiles，C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1 60 尝试后的.exe： 行 262"*
    1. **原因：**此问题由 Windows Defender 定义更新失败的或不完整后台下载。
    2. **解决方法：**自第一次更新尝试之后，请尝试恢复后最多 8 个小时更新已过去。

### <a name="known-issues-post-installation"></a>已知的问题 （安装后）

本部分包含安装后生成的已知的问题**20171122.1**。

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
- 当你尝试通过将项添加到 Azure 堆栈市场**从 Azure 中的添加**选项，并非所有的项可能下载可见。
- 用户可以浏览没有订阅，但完整的应用商店，并可以看到等计划，并提供管理项目。 这些项是向用户无法正常工作。

#### <a name="compute"></a>计算
- 为用户提供的选项以使用地域冗余存储创建虚拟机。 此配置导致虚拟机创建失败。
- 你可以配置虚拟机的可用性仅使用容错域之一，以及一个更新域设置。
- 没有任何应用商店体验，以创建虚拟机规模集。 你可以创建缩放集使用的模板。
- 缩放设置的虚拟机规模集不是在门户中提供的。 作为一种解决方法，你可以使用[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，你必须使用`-Name`参数而不是`-VMScaleSetName`。
 
#### <a name="networking"></a>联网
- 使用门户，不能具有公共 IP 地址创建负载平衡器。 一种解决方法，你可以使用 PowerShell 创建负载平衡器。
- 创建一个网络负载平衡时，必须创建网络地址转换 (NAT) 规则。 如果没有，你将收到错误，当你尝试创建负载平衡器后添加的 NAT 规则。
- 创建 VM 并将其与该 IP 地址相关联后，无法解除虚拟机 (VM) 中的公共 IP 地址的关联。 解除关联看起来工作，但之前分配的公共 IP 地址保留与原始 VM 相关联。 如果即使重新分配到新的 VM 的 IP 地址，则会发生此行为 (通常称为*VIP 交换*)。 所有未来都尝试连接通过在连接中，于最初关联的 VM，而不适用于新一个此 IP 地址结果。 目前，仅必须使用用于新 VM 创建的新公共 IP 地址。
- Azure 堆栈运算符可能不能部署、 删除、 修改 Vnet 或网络安全组。 在同一个包的后续更新尝试主要出现此问题。 这被引起更新其中我们正在调查打包问题。
 
#### <a name="sqlmysql"></a>SQL/MySQL
- 它可能需要一小时之前租户可以在新的 SQL 或 MySQL SKU 中创建数据库。 
- 直接在 SQL 和 MySQL 宿主服务器不由资源提供程序执行上的项目创建不支持，并且可能会导致不匹配的状态。
 
#### <a name="app-service"></a>应用服务
- 在订阅中创建其第一个 Azure 函数之前，用户必须注册存储资源提供程序。

#### <a name="identity"></a>标识

在 Azure Active Directory 联合身份验证服务 (ADFS) 已部署环境， **azurestack\azurestackadmin**帐户不再是默认提供程序订阅的所有者。 而不是登录到**管理门户 / adminmanagement 终结点**与**azurestack\azurestackadmin**，你可以使用**azurestack\cloudadmin**帐户，因此你可以管理并使用默认提供程序订阅。

> [!IMPORTANT]
> 即使**azurestack\cloudadmin**帐户是在 ADFS 部署环境中的默认提供程序订阅的所有者，它没有到 RDP 到主机的权限。 继续使用**azurestack\azurestackadmin**帐户或本地管理员帐户才能登录、 访问和管理主机，根据需要。

## <a name="download-the-update"></a>下载更新

你可以下载 Azure 堆栈 1711年更新包从[此处](https://aka.ms/azurestackupdatedownload)。

## <a name="more-information"></a>详细信息

Microsoft 提供了一种方法来监视和恢复使用特权终结点 (PEP) 与更新 1711年一起安装的更新。

- 请参阅[监视 Azure 堆栈使用特权终结点文档中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)。 

## <a name="see-also"></a>另请参阅

- 请参阅[管理 Azure 堆栈概述中的更新](azure-stack-updates.md)有关 Azure 堆栈中的更新管理的概述。
- 请参阅[在 Azure 堆栈中应用更新](azure-stack-apply-updates.md)有关如何使用 Azure 堆栈应用更新的详细信息。
