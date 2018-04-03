---
title: Azure Stack 1711 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统 1711 更新的功能、已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: f067656f510c14c4a2ac02d3629ae71432c342d2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1711-update"></a>Azure Stack 1711 更新

*适用于：Azure Stack 集成系统*

本文介绍此更新包中的改进与修复、此版本的已知问题，以及更新的下载位置。 已知问题分为直接与更新过程相关的已知问题，以及内部版本（安装后）相关的已知问题。

> [!IMPORTANT]
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1711 更新内部版本号为 **171201.3**。

## <a name="before-you-begin"></a>开始之前

### <a name="prerequisites"></a>必备组件

- 在应用此更新之前，必须先安装 Azure Stack [1710 更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710)。

- 查看使用**CloudAdmin**用作之前安装的帐户名称更新 1711年。 从版本 1711，开始*CloudAdmin*是保留的帐户的名称，不应手动指定。 在更新到版本 1711年时，此更新将消除 （通常称为 AzureStackAdmin） 的部署帐户的现有实例。 如果你已部署帐户命名为*CloudAdmin*，更新到 1711年将其删除。 

  *CloudAdmin*是内置帐户连接到[*特权终结点*](azure-stack-privileged-endpoint.md) (PEP)。 删除此帐户可能会导致 PEP 锁定，除非已经存在另一个是 CloudAdmin 组的成员的用户帐户。 

  如果 CloudAdmin 用作部署帐户的名称，将添加到你 PEP 新 CloudAdmin 用户，在启动到 1711 以避免锁定 Azure 堆栈外的更新之前。 若要添加新的 CloudAdmin 用户，请运行 cmdlet**新建 CloudAdminUser** PEP 上。

### <a name="new-features-and-fixes"></a>新功能和修复

此更新包含以下适用于 Azure Stack 的改进和修复。

#### <a name="new-features"></a>新增功能

- 支持联合解决方案模板
- Azure Stack 和 Graph 日志记录和错误处理的更新
- 打开和关闭主机的功能
- 用户现在可以自动激活 Windows VM
- 已添加特权终结点 PowerShell cmdlet 来检索 BitLocker 恢复密钥，以实现保留目的
- 支持在更新基础结构时更新脱机映像
- 使用“启用备份服务”来启用基础结构备份

#### <a name="fixes"></a>修复项

- 修复了现场更换单元 (FRU) 期间 DNS 中出现的资源争用情况，并更新了群集日志记录
- 修复了现场可更换单元 (FRU) 中禁用主机的重启功能
- 其他多项性能、安全性和稳定性修复

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 的新功能和修复

- [2017 年 11 月 14 日 — KB4048953（OS 内部版本 14393.1884）](https://support.microsoft.com/help/4048953)

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

本部分描述在安装 1711 更新期间可能会遇到的已知问题。


1. **症状：**Azure Stack 操作员在更新期间可能会看到以下错误：*"name:Install Update.", "description": "Install Update on Hosts and Infra VMs.", "errorMessage": "Type 'LiveUpdate' of Role 'VirtualMachines' raised an exception:\n\nThere is not enough space on the disk.\n\nat <ScriptBlock>, <No file>: line22", "status": "Error", "startTimeUtc": "2017-11-10T16:46:59.123Z", "endTimeUtc": "2017-11-10T19:20:29.669Z", "steps": [ ]"*
    2. **原因：**此问题的原因是属于 Azure Stack 基础结构的一个或多个虚拟机上的可用磁盘空间不足
    3. **解决方法：**请联系 Microsoft 客户服务和支持 (CSS) 寻求帮助。
<br><br>
2. **症状：**Azure Stack 操作员在更新程序期间可能看到以下错误：*Exception calling "ExtractToFile" with "3" argument(s):"The process cannot access the file '<\\<machineName>-ERCS01\C$\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll>'*
    1. **原因：**此问题的原因是通过门户恢复某项以前已使用特权结束点 (PEP) 恢复的更新。
    2. **解决方法：**请联系 Microsoft 客户服务和支持 (CSS) 寻求帮助。
<br><br>
3. **症状：** Azure 堆栈运算符在更新过程中可能会看到以下错误：*"角色 VirtualMachines 的类型 CheckHealth 引发的异常： \n\nVirtual 机运行状况检查<machineName>-ACS01 生成以下错误。 \nThere 时从主机中获取 VM 信息时出错。异常详细信息:\nGet-VM: 计算机 'Node03' 上的操作失败: WS-Management 服务无法处理请求。WMI \nservice 或 WMI 提供程序返回了未知的错误: HRESULT 0x8004106c”。*
    1. **原因：**此问题是由某个 Windows Server 问题造成的，后续的 Windows Server 更新中会解决此问题。
    2. **解决方法：**请联系 Microsoft 客户服务和支持 (CSS) 寻求帮助。
<br><br>
4. **症状：** Azure 堆栈运算符在更新过程中可能会看到以下错误：*"角色 URP 的类型 DefenderUpdate 引发了异常： 失败获取从版本\\SU1FileServer\SU1_Public\DefenderUpdates\x64\{文件名} 在复制 AzSDefenderFiles，C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1 60 尝试后的.exe： 行 262"*
    1. **原因：**此问题由于 Windows Defender 定义更新的后台下载失败或不完整所造成。
    2. **解决方法：**请在首次更新尝试起经过 8 小时之后，再尝试继续更新。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

本部分包含内部版本 **20171201.3** 的安装后已知问题。

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
- 尝试使用“从 Azure 添加”选项将项添加到 Azure Stack Marketplace 时，可能无法看到所有可供下载的项。
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

#### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

#### <a name="identity"></a>标识

在已部署 Azure Active Directory 联合身份身份验证服务 (ADFS) 的环境中，**azurestack\azurestackadmin** 帐户不再是默认提供商订阅的所有者。 如果不使用 **azurestack\azurestackadmin** 登录到**管理门户/adminmanagement 终结点**，可以使用 **azurestack\cloudadmin** 帐户，以便可以管理和使用默认提供商订阅。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帐户是 ADFS 部署环境中默认提供商订阅的所有者，它也没有通过 RDP 连接到主机的权限。 根据需要继续使用 **azurestack\azurestackadmin** 帐户或本地管理员帐户来登录、访问和管理主机。

#### <a name="infrastructure-backup-sevice"></a>基础结构备份服务
<!-- 1974890-->

- **1711 以前的备份不支持云恢复。**  
  1711 以前的备份与云恢复不兼容。 必须先更新到 1711，并启用备份。 若已启用备份，请确保在更新到 1711 后进行备份。 请删除 1711 以前的备份。

- **在 ASDK 上启用基础结构备份仅供测试用途。**  
  基础结构备份可用于还原多节点解决方案。 可以在 ASDK 上启用基础结构备份，但无法测试恢复。

有关详细信息，请参阅[使用基础结构备份服务对 Azure Stack 进行备份和数据恢复](azure-stack-backup-infrastructure-backup.md)。

## <a name="download-the-update"></a>下载更新

可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1711 更新包。

## <a name="more-information"></a>详细信息

Microsoft 已提供某种方式让用户使用装有 1711 更新的特权终结点 (PEP) 来监视和恢复更新。

- 请参阅文档：[使用特权终结点监视 Azure Stack 中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)。 

## <a name="see-also"></a>另请参阅

- 有关 Azure Stack 中的更新管理的概述，请参阅[有关在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
