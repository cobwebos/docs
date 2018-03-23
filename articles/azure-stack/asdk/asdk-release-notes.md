---
title: Microsoft Azure 堆栈开发工具包发行说明 |Microsoft 文档
description: Azure Stack 开发工具包的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6b08c1793857fd6c6a6a04c0d450e76a36357597
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
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
- 在管理员门户中[从下拉列表提交新的支持请求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)的功能不可用。 请改用以下链接：     
    - 对于 Azure 堆栈开发工具包，使用https://aka.ms/azurestackforum。    

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.  

- 查看资源或资源组的属性时，发现“移动”按钮已禁用。 这是预期的行为。 目前不支持在资源组或订阅之间移动资源或资源组。
 
- 你看到**所需的激活**建议您注册你的 Azure 堆栈开发工具包的警告性警报。 这是预期的行为。

- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

- 在管理门户的仪表板中，“更新”磁贴无法显示有关更新的信息。 若要解决此问题，请单击该磁贴对其进行刷新。

-   在管理门户中，你可能会看到 Microsoft.Update.Admin 组件严重警报。 警报名称、说明和修正均显示为：  
    - 错误 - FaultType 为 ResourceProviderTimeout 的模板缺失。

    可以放心地忽略此警报。 

- 在管理门户和用户门户上，概述边栏选项卡无法加载时选择与旧的 API 版本创建的存储帐户的概述边栏选项卡 (示例： 2015年-06-15)。 

  解决方法是，使用 PowerShell 运行**开始 ResourceSynchronization.ps1**脚本要恢复到存储帐户详细信息的访问权限。 [该脚本可从 GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)，必须使用服务管理员凭据运行开发工具包主机上如果使用 ASDK 和。  


#### <a name="health-and-monitoring"></a>运行状况和监视
在 Azure 堆栈管理员门户中，你可能会看到同名严重警报**挂起的外部证书到期**。  此警报可以安全地忽略，并且会影响 Azure 堆栈开发工具包的操作。 


#### <a name="marketplace"></a>Marketplace
- 用户无需订阅就能浏览整个 Marketplace，并且将会看到计划和产品等管理项。 对用户而言，这些项是非功能性的。
 
#### <a name="compute"></a>计算
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- Azure Stack 支持只使用固定类型的 VHD。 某些通过 Azure Stack 上的商城提供的映像使用动态 VHD，但这些映像已删除。 重设附加了动态磁盘的虚拟机 (VM) 的大小会导致该 VM 处于故障状态。

  若要解决此问题，请删除 VM，但不删除 VM 的磁盘（存储帐户中的 VHD Blob）。 然后将 VHD 从动态磁盘转换为固定磁盘，接着重新创建虚拟机。

- 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以尝试重新下载以前无法下载的 VM 映像。

-  如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings. 


#### <a name="networking"></a>网络
- 如果在“网络”下单击“连接”来设置 VPN 连接，会列出“VNet 到 VNet”作为可能的连接类型。 请不要选择此选项。 目前，仅支持“站点到站点(IPsec)”选项。

- 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。

- 内部负载均衡 (ILB) 对后端 VM 的 MAC 地址的处理不恰当，导致在后端网络上使用 Linux 实例时无法维持 ILB。  ILB 适用于后端网络上的 Windows 实例。

-   IP 转发功能在门户中可见，但启用 IP 转发却没有效果。 尚不支持此功能。

- Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。
 
- Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    针对应用服务的解决方法：如需通过远程桌面连接到控制器实例，请使用 PowerShell 修改网络安全组中的安全规则。  以下示例说明了如何先将配置设置为 *allow*，然后再还原为 *deny*： 
    
    - *Allow：*
 
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

    - *Deny：*

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
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。
 
#### <a name="usage-and-billing"></a>使用情况和计费
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell cmdlet 从 Github 下载 Azure Stack 工具时，收到一个错误：     
    -  invoke-webrequest: 请求已终止: 未能创建 SSL/TLS 安全通道。     

  之所以发生此错误，是因为最近的 GitHub 支持弃用了 Tlsv1 和 Tlsv1.1 加密标准（PowerShell 的默认设置）。 有关详细信息，请参阅 [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/)（弱加密标准删除通知）。

  若要解决此问题，请将 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 添加到脚本顶部，强制 PowerShell 控制台在从 GitHub 存储库下载项目时使用 TLSv1.2。


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
- 从版本 1711，开始**CloudAdmin**是保留的帐户的名称，不应手动指定部署的开发工具包时。 

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

