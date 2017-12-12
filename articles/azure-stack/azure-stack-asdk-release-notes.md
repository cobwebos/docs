---
title: "Microsoft Azure 堆栈开发工具包发行说明 |Microsoft 文档"
description: "改进、 修复和 Azure 堆栈开发工具包的已知的问题。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: mabrigg
ms.openlocfilehash: ca966dd4664861f476401c5691ab73b95d3f5a2b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure 堆栈开发工具包发行说明

*适用范围： Azure 堆栈开发工具包*

这些发行说明提供有关改进、 修复和 Azure 堆栈开发工具包中的已知的问题的信息。 如果你不确定你正在运行哪个版本，则可以[使用门户检查](azure-stack-updates.md#determine-the-current-version)。

## <a name="build-201711221"></a>生成 20171122.1

### <a name="new-features-and-fixes"></a>新功能和修补程序

- 请参阅[新功能和修复](azure-stack-update-1711.md#new-features-and-fixes)Azure 堆栈的 Azure 堆栈 1711年更新发行说明的部分集成系统。

    > [!IMPORTANT]
    > 中列出的项的某些**新功能和修复**部分中有仅与 Azure 堆栈集成系统。

### <a name="known-issues"></a>已知问题
 
#### <a name="deployment"></a>部署
- 必须在部署期间通过 IP 地址中指定的时间服务器。

#### <a name="infrastructure-management"></a>基础结构管理
- 不要在上启用基础结构备份**基础结构备份**边栏选项卡。
- 缩放单元节点的基本信息中不显示基板管理控制器 (BMC) IP 地址和模型。 Azure 堆栈开发工具包中需要此行为。

#### <a name="portal"></a>门户
- 你可能会看到在门户中的空白仪表板。 若要恢复仪表板，门户中，右上角选择齿轮图标，然后选择**还原默认设置**。
- 在查看资源组的属性时**移动**按钮处于禁用状态。 此行为被预期行为。 当前不支持订阅之间移动资源组。
-  对于任何工作流，其中在下拉列表中选择订阅、 资源组或位置，你可能会遇到一个或多个以下问题：

   - 你可能会看到在列表顶部的空行。 你仍应将能够按预期方式选择一个项。
   - 如果下拉列表中项的列表很短，可能无法查看任何项名称。
   - 如果你有多个用户订阅，资源组下拉列表可能为空。 

   若要解决的最后两个问题，你可键入名称的订阅或资源组 （如果你知道它），也可以改为使用 PowerShell。

- 你将看到**所需的激活**建议您注册你的 Azure 堆栈开发工具包的警告性警报。 此行为被预期行为。
- 如果**组件**从任何单击链接**基础结构角色**警报，生成**概述**边栏选项卡中尝试进行加载，并且无法正常工作。 此外 * * 概述 * * 边栏选项卡不会超时。
- 删除孤立资源导致的用户订阅。 解决方法是，首先删除用户资源或整个资源组中，，然后再删除用户订阅。
- 你不能通过使用 Azure 堆栈门户查看到你的订阅的权限。 一种解决方法，可以通过使用 PowerShell 来验证权限。
 
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
- 下**网络**，如果你单击**连接**设置 VPN 连接， **VNet 到 VNet**列为可能的连接类型。 不选择此选项。 目前，仅**站点到站点 (IPsec)**支持选项。
- 创建 VM 并将其与该 IP 地址相关联后，无法解除虚拟机 (VM) 中的公共 IP 地址的关联。 解除关联看起来工作，但之前分配的公共 IP 地址保留与原始 VM 相关联。 如果即使重新分配到新的 VM 的 IP 地址，则会发生此行为 (通常称为*VIP 交换*)。 所有未来都尝试连接通过在连接中，于最初关联的 VM，而不适用于新一个此 IP 地址结果。 目前，仅必须使用用于新 VM 创建的新公共 IP 地址。
- Azure 堆栈运算符可能不能部署、 删除、 修改 Vnet 或网络安全组。 在同一个包的后续更新尝试主要出现此问题。 这被引起更新其中我们正在调查打包问题。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 它可能需要一小时之前租户可以在新的 SQL 或 MySQL SKU 中创建数据库。 
- 直接在 SQL 和 MySQL 宿主服务器不由资源提供程序执行上的项目创建不支持，并且可能会导致不匹配的状态。

#### <a name="app-service"></a>应用服务
- 在订阅中创建其第一个 Azure 函数之前，用户必须注册存储资源提供程序。
 
#### <a name="usage-and-billing"></a>使用情况和计费
- 公共 IP 地址使用情况计数数据演示如何将相同*EventDateTime*而不是每个记录的值*TimeDate*显示时已创建了记录的 stamp。 目前，不能使用此数据来执行的公共 IP 地址使用情况的准确记帐。

#### <a name="identity"></a>标识

在 Azure Active Directory 联合身份验证服务 (ADFS) 已部署环境， **azurestack\azurestackadmin**帐户不再是默认提供程序订阅的所有者。 而不是登录到**管理门户 / adminmanagement 终结点**与**azurestack\azurestackadmin**，你可以使用**azurestack\cloudadmin**帐户，因此你可以管理并使用默认提供程序订阅。

> [!IMPORTANT]
> 即使**azurestack\cloudadmin**帐户是在 ADFS 部署环境中的默认提供程序订阅的所有者，它没有到 RDP 到主机的权限。 继续使用**azurestack\azurestackadmin**帐户或本地管理员帐户才能登录、 访问和管理主机，根据需要。


## <a name="build-201710201"></a>生成 20171020.1

### <a name="improvements-and-fixes"></a>改进和修复

若要查看的改进和 20171020.1 生成中的修复的列表，请参阅[改进和修复](azure-stack-update-1710.md#improvements-and-fixes)Azure 堆栈的 1710年发行说明的部分集成系统。 某些"其他质量的改进和修补程序"一节中列出的项是仅与集成的系统。

此外，已进行了下列修补程序：
- 修复了问题的计算资源提供程序在其中显示未知的状态。
- 修复了问题其中配额可能不会显示在管理员门户后您创建它们，然后尝试查看计划的详细信息。

### <a name="known-issues"></a>已知问题

#### <a name="powershell"></a>PowerShell
- AzureRM 1.2.11 PowerShell 模块的版本附带的重大更改的列表。 有关从 1.2.10 升级版本，请参阅[迁移指南](https://aka.ms/azspowershellmigration)。
 
#### <a name="deployment"></a>部署
- 必须在部署期间通过 IP 地址中指定的时间服务器。

#### <a name="infrastructure-management"></a>基础结构管理
- 不要在上启用基础结构备份**基础结构备份**边栏选项卡。
- 缩放单元节点的基本信息中不显示基板管理控制器 (BMC) IP 地址和模型。 Azure 堆栈开发工具包中需要此行为。

#### <a name="portal"></a>门户
- 你可能会看到在门户中的空白仪表板。 若要恢复仪表板，门户中，右上角选择齿轮图标，然后选择**还原默认设置**。
- 在查看资源组的属性时**移动**按钮处于禁用状态。 此行为被预期行为。 当前不支持订阅之间移动资源组。
-  对于任何工作流，其中在下拉列表中选择订阅、 资源组或位置，你可能会遇到一个或多个以下问题：

   - 你可能会看到在列表顶部的空行。 你仍应将能够按预期方式选择一个项。
   - 如果下拉列表中项的列表很短，可能无法查看任何项名称。
   - 如果你有多个用户订阅，资源组下拉列表可能为空。 

   若要解决的最后两个问题，你可键入名称的订阅或资源组 （如果你知道它），也可以改为使用 PowerShell。

- 你将看到**所需的激活**建议您注册你的 Azure 堆栈开发工具包的警告性警报。 此行为被预期行为。
- 在**所需的激活**警告警报详细信息，请不要单击的链接**AzureBridge**组件。 如果这样做，**概述**边栏选项卡将不成功尝试加载，且不会超时。
- 在管理员门户中，你可能会看到**错误提取租户**中的错误**通知**区域。 你可以放心地忽略此错误。
- 删除孤立资源导致的用户订阅。 解决方法是，首先删除用户资源或整个资源组中，，然后再删除用户订阅。
- 你不能通过使用 Azure 堆栈门户查看到你的订阅的权限。 一种解决方法，可以通过使用 PowerShell 来验证权限。
 
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
- 下**网络**，如果你单击**连接**设置 VPN 连接， **VNet 到 VNet**列为可能的连接类型。 不选择此选项。 目前，仅**站点到站点 (IPsec)**支持选项。
- 创建 VM 并将其与该 IP 地址相关联后，无法解除虚拟机 (VM) 中的公共 IP 地址的关联。 解除关联看起来工作，但之前分配的公共 IP 地址保留与原始 VM 相关联。 如果即使重新分配到新的 VM 的 IP 地址，则会发生此行为 (通常称为*VIP 交换*)。 所有未来都尝试连接通过在连接中，于最初关联的 VM，而不适用于新一个此 IP 地址结果。 目前，仅必须使用用于新 VM 创建的新公共 IP 地址。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 它可能需要一小时之前租户可以在新的 SQL 或 MySQL SKU 中创建数据库。 
- 直接在 SQL 和 MySQL 宿主服务器不由资源提供程序执行上的项目创建不支持，并且可能会导致不匹配的状态。

#### <a name="app-service"></a>应用服务
- 在订阅中创建其第一个 Azure 函数之前，用户必须注册存储资源提供程序。
 
#### <a name="usage-and-billing"></a>使用情况和计费
- 公共 IP 地址使用情况计数数据演示如何将相同*EventDateTime*而不是每个记录的值*TimeDate*显示时已创建了记录的 stamp。 目前，不能使用此数据来执行的公共 IP 地址使用情况的准确记帐。

## <a name="build-201709283"></a>生成 20170928.3

### <a name="known-issues"></a>已知问题

#### <a name="powershell"></a>PowerShell
- AzureRM 1.2.11 PowerShell 模块的版本附带的重大更改的列表。 有关从 1.2.10 升级版本，请参阅[迁移指南](https://aka.ms/azspowershellmigration)。

#### <a name="deployment"></a>部署
- 必须在部署期间通过 IP 地址中指定的时间服务器。

 #### <a name="infrastructure-management"></a>基础结构管理
- 不要在上启用基础结构备份**基础结构备份**边栏选项卡。
- 计算资源提供程序显示未知的状态。
- 缩放单元节点的基本信息中不显示基板管理控制器 (BMC) IP 地址和模型。 Azure 堆栈开发工具包中需要此行为。 
   
#### <a name="portal"></a>门户
- 你可能会看到在门户中的空白仪表板。 若要恢复仪表板，门户中，右上角选择齿轮图标，然后选择**还原默认设置**。
- 在查看资源组的属性时**移动**按钮处于禁用状态。 此行为被预期行为。 当前不支持订阅之间移动资源组。
- 你将看到**所需的激活**建议您注册你的 Azure 堆栈开发工具包的警告性警报。 此行为被预期行为。
- 在**所需的激活**警告警报详细信息，请不要单击的链接**AzureBridge**组件。 如果这样做，**概述**边栏选项卡将不成功尝试加载，且不会超时。
- 配额可能不会显示在管理员门户后你创建它们，然后尝试稍后再查看计划详细信息。 解决方法是，在**服务和配额**，单击**添加**，并添加新条目。
- 删除孤立资源导致的用户订阅。 解决方法是，首先删除用户资源或整个资源组中，，然后再删除用户订阅。
- 你不能查看你的订阅使用 Azure 堆栈门户的权限。 一种解决方法，可以通过使用 Powershell 来验证权限。
  
#### <a name="marketplace"></a>Marketplace
- 用户可以浏览没有订阅，但完整的应用商店，并可以看到等计划，并提供管理项目。 这些项是向用户无法正常工作。
 
#### <a name="compute"></a>计算
- 为用户提供的选项以使用地域冗余存储创建虚拟机。 此配置导致虚拟机创建失败。
- 你可以配置虚拟机的可用性仅使用容错域之一，以及一个更新域设置。
- 没有任何应用商店体验，以创建虚拟机规模集。 你可以创建缩放集使用的模板。

#### <a name="networking"></a>联网
- 使用门户，不能具有公共 IP 地址创建负载平衡器。 一种解决方法，你可以使用 PowerShell 创建负载平衡器。
- 创建一个网络负载平衡时，必须创建网络地址转换 (NAT) 规则。 如果没有，你将收到错误，当你尝试创建负载平衡器后添加的 NAT 规则。
- 下**网络**，如果你单击**连接**设置 VPN 连接， **VNet 到 VNet**列为可能的连接类型。 不选择此选项。 目前，仅**站点到站点 (IPsec)**支持选项。
- 创建 VM 并将其与该 IP 地址相关联后，无法解除虚拟机 (VM) 中的公共 IP 地址的关联。 解除关联看起来工作，但之前分配的公共 IP 地址保留与原始 VM 相关联。 如果即使重新分配到新的 VM 的 IP 地址，则会发生此行为 (有时称为*VIP 交换*)。 所有未来都尝试连接通过在连接中，于最初关联的 VM，而不适用于新一个此 IP 地址结果。 目前，仅必须使用用于新 VM 创建的新公共 IP 地址。

#### <a name="sqlmysql"></a>SQL/MySQL
- 它可能需要一小时之前租户可以在新的 SQL 或 MySQL SKU 中创建数据库。 
- 直接在 SQL 和 MySQL 宿主服务器不由资源提供程序执行上的项目创建不支持，并且可能会导致不匹配的状态。

#### <a name="app-service"></a>应用服务
- 在订阅中创建其第一个 Azure 函数之前，用户必须注册存储资源提供程序。
