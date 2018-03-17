---
title: "Microsoft Azure 堆栈开发工具包发行说明 |Microsoft 文档"
description: "Azure Stack 开发工具包的改进、修复和已知问题。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 176b850120958a5ca5fdaece4831e2ed27ac0a04
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 开发工具包发行说明
这些发行说明提供 Azure Stack 开发工具包的改进、修复和已知问题的相关信息。 如果不确定所运行的版本，可以[使用门户检查版本](.\.\azure-stack-updates.md#determine-the-current-version)。

> 始终应用最新的最新信息在 ASDK 通过订阅到[ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [馈送](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)。

## <a name="build-201803021"></a>生成 20180302.1

### <a name="new-features-and-fixes"></a>新功能和修复
请参阅[新功能和修复](.\.\azure-stack-update-1802.md#new-features-and-fixes)Azure 堆栈的 Azure 堆栈 1802年更新发行说明的部分集成系统。

> [!IMPORTANT]    
> **新功能和修复**部分所列的某些项仅与 Azure Stack 集成系统相关。


### <a name="known-issues"></a>已知问题
 
#### <a name="portal"></a>门户
- 功能[从下拉列表中打开新的支持请求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)从内管理员门户不可用。 相反，使用以下链接：     
    - 对于 Azure 堆栈开发工具包，使用https://aka.ms/azurestackforum。    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- 当您查看的资源或资源组的属性时**移动**按钮处于禁用状态。 这是预期的行为。 当前不支持资源组或订阅之间移动资源或资源组。
 
- 你看到**所需的激活**建议您注册你的 Azure 堆栈开发工具包的警告性警报。 这是预期的行为。

- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- 到你的订阅使用 Azure 堆栈门户，你无法查看权限。 解决方法是，使用 PowerShell 来验证权限。

- 在管理门户的仪表板中，更新磁贴无法正常显示有关更新的信息。 若要解决此问题，请单击该磁贴以对其进行刷新。

-   在管理门户中，你可能会看到 Microsoft.Update.Admin 组件严重警报。 警报名称、 说明和修正所有显示为：  
    - *错误-FaultType ResourceProviderTimeout 模板找不到。*

    可以安全地忽略此警报。 

#### <a name="health-and-monitoring"></a>运行状况和监视
在 Azure 堆栈管理员门户中，你可能会看到同名严重警报**挂起的外部证书到期**。  此警报可以安全地忽略，并且会影响 Azure 堆栈开发工具包的操作。 


#### <a name="marketplace"></a>Marketplace
- 用户无需订阅就能浏览整个 Marketplace，并且将会看到计划和产品等管理项。 对用户而言，这些项是非功能性的。
 
#### <a name="compute"></a>计算
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- Azure 堆栈支持仅使用固定的类型的 Vhd。 通过 Azure 堆栈上应用商店提供一些映像使用动态 Vhd，但删除这些。 动态磁盘附加到它的大小调整虚拟机 (VM) 使处于失败状态的 VM。

  若要解决此问题，请删除 VM，但不删除 VM 的磁盘（存储帐户中的 VHD Blob）。 然后将 VHD 从动态磁盘转换为固定磁盘，然后重新创建虚拟机。

- 当 Azure 堆栈用户门户上创建虚拟机时，门户将显示可以将附加到的 DS 系列 VM 的数据磁盘数目不正确。 DS 系列 Vm 可以容纳尽可能多的数据磁盘作为 Azure 配置。

- 当 VM 映像失败时创建时，则可能会将无法删除失败的项添加到 VM 映像计算边栏选项卡中。

  解决方法是，使用可以通过 HYPER-V 创建的 dummy VHD 创建新的 VM 映像 (新建 VHD 的路径 C:\dummy.vhd-固定-SizeBytes 1 GB)。 此过程来解决该问题阻止删除失败的项。 然后，创建该虚拟映像后 15 分钟成功删除它。

  然后，可以尝试重新下载以前失败的 VM 映像。

-  如果预配 VM 部署中的扩展所用时间过长，用户应让而不是尝试停止解除分配或删除 VM 的过程设置超时。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>网络
- 如果在“网络”下单击“连接”来设置 VPN 连接，会列出“VNet 到 VNet”作为可能的连接类型。 请不要选择此选项。 目前，仅支持“站点到站点(IPsec)”选项。

- 创建 VM 并将其与公共 IP 地址相关联后，你无法解除该虚拟机与该 IP 地址的关联。 解除关联看似工作，但之前分配的公共 IP 地址仍保留与原始 VM 相关联。

  目前，你必须使用仅新公共 IP 地址为你创建的新 Vm。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。

- 内部负载平衡 (ILB) 不正确 MAC 地址为处理后端 Vm，这将导致产生 ILB 在后端网络上使用 Linux 实例时中断。  ILB Windows 实例，并用在上正常工作后端网络。

-   IP 转发功能在门户中可见，但是启用 IP 转发不起作用。 尚不支持此功能。

- Azure 堆栈支持单个*本地网络网关*每个 IP 地址。 这是 true 跨所有租户订阅。 创建第一个的本地网络网关连接，后续尝试创建具有相同的 IP 地址的本地网络网关资源后将被阻止。

- 已创建了 DNS 服务器设置的虚拟网络上*自动*、 更改为自定义的 DNS 服务器失败。 更新的设置不推送到该 Vnet 中的 Vm 中。
 
- Azure 堆栈不支持将其他网络接口添加到的 VM 实例，该 VM 部署后。 如果 VM 需要多个网络接口，它们必须在部署时定义它。

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    App Service 的解决方法： 如果你需要远程桌面连接到控制器实例，则修改中使用 PowerShell 的网络安全组的安全规则。  下面是如何的示例*允许*，然后将还原到的配置*拒绝*: 
    
    - *允许使用：*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *拒绝：*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```


#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- 可能需要最多一小时，才能在用户可以在新的 SQL 或 MySQL SKU 中创建数据库。

- 托管服务器的数据库必须被专用的资源提供程序和用户工作负荷。 不能使用任何其他使用者，包括应用程序服务正在使用的实例。

#### <a name="app-service"></a>应用服务
- 在订阅中创建其第一个 Azure 函数之前，用户必须注册存储资源提供程序。

- 以横向扩展基础结构 （工作进程、 管理、 前端角色），你必须使用 PowerShell 中所述的发行说明的计算。
 
#### <a name="usage-and-billing"></a>使用情况和计费
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure 堆栈工具
- 使用时*调用 webrequest* PowerShell cmdlet 来下载 Azure 堆栈工具从 Github，您将收到错误：     
    -  *调用 webrequest： 该请求已中止： 无法创建 SSL/TLS 安全通道。*     

  由于新的 GitHub 支持弃用 Tlsv1 和 Tlsv1.1 加密标准 （PowerShell 的默认值） 的发生此错误。 有关详细信息，请参阅[弱加密标准删除通知](https://githubengineering.com/crypto-removal-notice/)。

  若要解决此问题，将添加`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`到要强制 PowerShell 控制台 TLSv1.2 下载时要使用从 GitHub 存储库的脚本的顶部。


## <a name="build-201801032"></a>内部版本 20180103.2

### <a name="new-features-and-fixes"></a>新功能和修复

- 请参阅 Azure Stack 集成系统的 Azure Stack 1712 更新版发行说明的[新功能和修复](.\.\azure-stack-update-1712.md#new-features-and-fixes)部分。

    > [!IMPORTANT]
    > **新功能和修复**部分所列的某些项仅与 Azure Stack 集成系统相关。

### <a name="known-issues"></a>已知问题
 
#### <a name="deployment"></a>部署
- 在部署期间，必须使用 IP 地址指定时间服务器。

#### <a name="infrastructure-management"></a>基础结构管理
- 请不要在“基础架构备份”边栏选项卡上启用基础结构备份。
- 缩放单位节点的概要信息中不显示基础板管理控制器 (BMC) IP 地址和模型。 这是 Azure Stack 开发工具包中的预期行为。

#### <a name="portal"></a>门户
- 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。
- 查看资源组的属性时，“移动”按钮已禁用。 这是预期的行为。 目前不支持在订阅之间移动资源组。
-  对于可在下拉列表中选择订阅、资源组或位置的任何工作流，可能会遇到一个或多个以下问题：

   - 可能会在列表顶部看到一个空白行。 应该仍能按预期选择项。
   - 如果下拉列表中的项列表很短，可能无法查看任何项名称。
   - 如果有多个用户订阅，资源组的下拉列表可能是空的。 

   若要解决后两个问题，可以输入订阅或资源组的名称（如果知道），或者可以改用 PowerShell。

- 看到“需要激活”警告警报，提示注册 Azure Stack 开发工具包。 这是预期的行为。
- 如果在任何“基础结构角色”警报中单击“组件”链接，生成的“概述”边栏选项卡会尝试加载但失败。 此外，“概述”边栏选项卡不会超时。
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。
 
#### <a name="marketplace"></a>Marketplace
- 出于兼容性考虑，此版本中将会删除一些 Marketplace 项。 在进一步验证后，会重新启用这些项。
- 用户无需订阅就能浏览整个 Marketplace，并且将会看到计划和产品等管理项。 对用户而言，这些项是非功能性的。
 
#### <a name="compute"></a>计算
- 用户可以使用相应的选项创建包含异地冗余存储的虚拟机。 此配置会导致虚拟机创建失败。 
- 可以配置只包含一个容错域和一个更新域的虚拟机可用性集。
- 没有任何可用于创建虚拟机规模集的 Marketplace 体验。 可以使用模板来创建规模集。
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

#### <a name="networking"></a>网络
- 无法使用门户创建采用公共 IP 地址的负载均衡器。 解决方法是使用 PowerShell 创建负载均衡器。
- 创建网络负载均衡器时，必须创建网络地址转换 (NAT) 规则。 否则，在创建负载均衡器之后尝试添加 NAT 规则时会收到错误。
- 如果在“网络”下单击“连接”来设置 VPN 连接，会列出“VNet 到 VNet”作为可能的连接类型。 请不要选择此选项。 目前，仅支持“站点到站点(IPsec)”选项。
- 创建 VM 并与公共 IP 地址创建关联之后，无法取消该 IP 地址与虚拟机 (VM) 的关联。 取消关联看似正常运行，但以前分配的公共 IP 地址仍与原始 VM 相关联。 即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。 目前只有在创建新的 VM 时，才能使用新的公共 IP 地址。
- Azure Stack 操作员可能无法部署、删除、修改 VNET 或网络安全组。 此问题主要出现于同一个包的后续更新尝试。 此问题的原因是发生了目前正在调查的更新打包问题。
- 内部负载均衡 (ILB) 对 MAC 地址的后端 VM 进行不恰当的处理，导致 Linux 实例损坏。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 最长可能需要在一小时后，租户才能在新的 SQL 或 MySQL SKU 中创建数据库。 
- 不支持在 SQL 和 MySQL 托管服务器中直接创建不是由资源提供程序执行的项，这可能导致不匹配的状态。

#### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。
 
#### <a name="usage-and-billing"></a>使用情况和计费
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

#### <a name="identity"></a>标识

在已部署 Azure Active Directory 联合身份身份验证服务 (ADFS) 的环境中，**azurestack\azurestackadmin** 帐户不再是默认提供程序订阅的所有者。 如果不使用 **azurestack\azurestackadmin** 登录到**管理门户/adminmanagement 终结点**，可以使用 **azurestack\cloudadmin** 帐户，以便可以管理和使用默认提供程序订阅。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帐户是 ADFS 部署环境中默认提供程序订阅的所有者，它也没有通过 RDP 连接到主机的权限。 根据需要继续使用 **azurestack\azurestackadmin** 帐户或本地管理员帐户来登录、访问和管理主机。


## <a name="build-201711221"></a>内部版本 20171122.1

### <a name="new-features-and-fixes"></a>新功能和修复

- 请参阅 Azure Stack 集成系统的 Azure Stack 1711 更新版发行说明的[新功能和修复](.\.\azure-stack-update-1711.md#new-features-and-fixes)部分。

    > [!IMPORTANT]
    > **新功能和修复**部分所列的某些项仅与 Azure Stack 集成系统相关。

### <a name="known-issues"></a>已知问题
 
#### <a name="deployment"></a>部署
- 在部署期间，必须使用 IP 地址指定时间服务器。

#### <a name="infrastructure-management"></a>基础结构管理
- 请不要在“基础架构备份”边栏选项卡上启用基础结构备份。
- 缩放单位节点的概要信息中不显示基础板管理控制器 (BMC) IP 地址和模型。 这是 Azure Stack 开发工具包中的预期行为。

#### <a name="portal"></a>门户
- 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。
- 查看资源组的属性时，“移动”按钮已禁用。 这是预期的行为。 目前不支持在订阅之间移动资源组。
-  对于可在下拉列表中选择订阅、资源组或位置的任何工作流，可能会遇到一个或多个以下问题：

   - 可能会在列表顶部看到一个空白行。 应该仍能按预期选择项。
   - 如果下拉列表中的项列表很短，可能无法查看任何项名称。
   - 如果有多个用户订阅，资源组的下拉列表可能是空的。 

   若要解决后两个问题，可以输入订阅或资源组的名称（如果知道），或者可以改用 PowerShell。

- 看到“需要激活”警告警报，提示注册 Azure Stack 开发工具包。 这是预期的行为。
- 如果在任何“基础结构角色”警报中单击“组件”链接，生成的“概述”边栏选项卡会尝试加载但失败。 此外，“概述”边栏选项卡不会超时。
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。
 
#### <a name="marketplace"></a>Marketplace
- 尝试使用“从 Azure 添加”选项将项添加到 Azure Stack Marketplace 时，可能无法看到所有可供下载的项。
- 用户无需订阅就能浏览整个 Marketplace，并且将会看到计划和产品等管理项。 对用户而言，这些项是非功能性的。
 
#### <a name="compute"></a>计算
- 用户可以使用相应的选项创建包含异地冗余存储的虚拟机。 此配置会导致虚拟机创建失败。 
- 可以配置只包含一个容错域和一个更新域的虚拟机可用性集。
- 没有任何可用于创建虚拟机规模集的 Marketplace 体验。 可以使用模板来创建规模集。
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

#### <a name="networking"></a>网络
- 无法使用门户创建采用公共 IP 地址的负载均衡器。 解决方法是使用 PowerShell 创建负载均衡器。
- 创建网络负载均衡器时，必须创建网络地址转换 (NAT) 规则。 否则，在创建负载均衡器之后尝试添加 NAT 规则时会收到错误。
- 如果在“网络”下单击“连接”来设置 VPN 连接，会列出“VNet 到 VNet”作为可能的连接类型。 请不要选择此选项。 目前，仅支持“站点到站点(IPsec)”选项。
- 创建 VM 并与公共 IP 地址创建关联之后，无法取消该 IP 地址与虚拟机 (VM) 的关联。 取消关联看似正常运行，但以前分配的公共 IP 地址仍与原始 VM 相关联。 即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。 目前只有在创建新的 VM 时，才能使用新的公共 IP 地址。
- Azure Stack 操作员可能无法部署、删除、修改 VNET 或网络安全组。 此问题主要出现于同一个包的后续更新尝试。 此问题的原因是发生了目前正在调查的更新打包问题。
- 内部负载均衡 (ILB) 对 MAC 地址的后端 VM 进行不恰当的处理，导致 Linux 实例损坏。
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- 最长可能需要在一小时后，租户才能在新的 SQL 或 MySQL SKU 中创建数据库。 
- 不支持在 SQL 和 MySQL 托管服务器中直接创建不是由资源提供程序执行的项，这可能导致不匹配的状态。

    > [!NOTE]
    > 分别参阅 [SQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy) 和 [MySQL](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy) 安装文章，获取版本兼容性指南。

#### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。
 
#### <a name="usage-and-billing"></a>使用情况和计费
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

#### <a name="identity"></a>标识

在已部署 Azure Active Directory 联合身份身份验证服务 (ADFS) 的环境中，**azurestack\azurestackadmin** 帐户不再是默认提供程序订阅的所有者。 如果不使用 **azurestack\azurestackadmin** 登录到**管理门户/adminmanagement 终结点**，可以使用 **azurestack\cloudadmin** 帐户，以便可以管理和使用默认提供程序订阅。

> [!IMPORTANT]
> 即使 **azurestack\cloudadmin** 帐户是 ADFS 部署环境中默认提供程序订阅的所有者，它也没有通过 RDP 连接到主机的权限。 根据需要继续使用 **azurestack\azurestackadmin** 帐户或本地管理员帐户来登录、访问和管理主机。

