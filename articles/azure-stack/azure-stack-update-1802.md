---
title: "Azure 堆栈 1802年更新 |Microsoft 文档"
description: "了解什么是 Azure 堆栈 1802年更新中集成的系统、 已知的问题和下载更新的位置。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 247f13717971d3660b3ec0ee94821bd593c5fed0
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-1802-update"></a>Azure 堆栈 1802年更新

*适用于：Azure Stack 集成系统*

本文介绍改进和 1802年更新包的已知问题有关此版本中，以及在何处下载此更新中修复。 已知的问题划分为与更新过程直接相关的问题和问题生成 （安装后）。

> [!IMPORTANT]        
> 此更新包项仅用于集成 Azure 堆栈系统。 不适用于此更新包 Azure 堆栈开发工具包。

## <a name="build-reference"></a>生成参考    
Azure 堆栈 1802年更新生成号是**20180302.1**。  


## <a name="before-you-begin"></a>开始之前    
> [!IMPORTANT]    
> 不要尝试在此更新的安装过程中创建虚拟机。 有关管理更新的详细信息，请参阅[管理 Azure 堆栈概述中的更新](/azure-stack-updates#plan-for-updates)。


### <a name="prerequisites"></a>必备组件
- 安装 Azure 堆栈[1712年更新](azure-stack-update-1712.md)应用 Azure 堆栈 1802年更新前。    

- 安装**AzS 修补程序 – 1.0.180312.1-生成 20180222.2**应用 Azure 堆栈 1802年更新前。 此修补程序更新 Windows Defender，并为 Azure 堆栈下载更新时可用。

  若要安装该修补程序，请执行的正常过程[为 Azure 堆栈安装更新](azure-stack-apply-updates.md)。 更新的名称显示为**AzS 修补程序 – 1.0.180312.1**，并包括以下文件： 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  将这些文件上载到存储帐户和容器之后, 从管理门户中的更新磁贴中运行安装。 
  
  与不同 Azure 堆栈的更新，安装此更新不会更改 Azure 堆栈的版本。  若要确认此更新已安装，查看的列表**已安装的更新**。
 


### <a name="post-update-steps"></a>更新后步骤
*有更新 1802年无更新后的步骤。*


### <a name="new-features-and-fixes"></a>新功能和修复
此更新包括以下改进和修复 Azure 堆栈。

- **针对以下的 Azure 存储服务 API 版本添加了支持**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    有关详细信息，请参阅[Azure 堆栈存储： 差异和注意事项](/azure/azure-stack/user/azure-stack-acs-differences)。

- **支持更大[块 Blob](azure-stack-acs-differences.md)**:
    - 允许的最大块大小从 4 MB 增加到 100 MB。
    - 最大 blob 大小从 195 GB 增加到 4.75 TB。  

- **基础结构备份**现在显示在资源提供程序磁贴，并且备份已启用的警报。 有关基础结构备份服务的详细信息，请参阅[基础结构备份服务的 Azure 堆栈的备份和数据恢复](azure-stack-backup-infrastructure-backup.md)。

- **更新到*测试 AzureStack* cmdlet**以提高诊断存储。 有关此 cmdlet 的详细信息，请参阅[验证 Azure 堆栈](azure-stack-diagnostic-test.md)。

- **基于角色的访问控制 (RBAC) 改进**-你现在可以使用 RBAC 将权限委派给通用用户组时使用 AD FS 部署 Azure 堆栈。 若要了解有关 RBAC 的详细信息，请参阅[管理 RBAC](azure-stack-manage-permissions.md)。

- **为多个容错域中添加了支持**。  有关详细信息，请参阅[Azure 堆栈的高可用性](azure-stack-key-features.md#high-availability-for-azure-stack)。

- **各种修复**的性能、 稳定性、 安全性和使用由 Azure 堆栈的操作系统。

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>在更新过程的已知的问题    
*没有为在安装更新 1802年已知的问题。*


### <a name="known-issues-post-installation"></a>已知的问题 （安装后）
以下是安装后的生成的已知的问题**20180302.1**

#### <a name="portal"></a>门户
- 功能[从下拉列表中打开新的支持请求](azure-stack-manage-portals.md#quick-access-to-help-and-support)从内管理员门户不可用。 相反，使用以下链接：     
    - 对于 Azure 堆栈集成的系统，使用 https://aka.ms/newsupportrequest。

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- 它可能无法在管理员门户中查看计算或存储资源。 此问题的原因会导致更新会错误地报告为成功的更新安装过程时出错。 如果出现此问题，请与 Microsoft 客户支持服务以获取帮助。

- 你可能会看到在门户中的空白仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。

- 当您查看的资源或资源组的属性时**移动**按钮处于禁用状态。 这是预期的行为。 当前不支持资源组或订阅之间移动资源或资源组。

- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- 到你的订阅使用 Azure 堆栈门户，你无法查看权限。 解决方法是，使用 PowerShell 来验证权限。

- 在管理门户的仪表板中，更新磁贴无法正常显示有关更新的信息。 若要解决此问题，请单击该磁贴以对其进行刷新。

-   在管理门户中可能会看到 Microsoft.Update.Admin 组件严重警报。 警报名称、 说明和修正所有显示为：  
    - *错误-FaultType ResourceProviderTimeout 模板找不到。*

    可以安全地忽略此警报。 

#### <a name="health-and-monitoring"></a>运行状况和监视
在更新到 1802年后没有已知的问题。

#### <a name="marketplace"></a>Marketplace
- 用户可以浏览而无需订阅完整应用商店，并可以看到等计划，并提供管理项目。 对用户而言，这些项是非功能性的。

#### <a name="compute"></a>计算
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- Azure 堆栈支持仅使用固定的类型的 Vhd。 通过 Azure 堆栈上应用商店提供一些映像使用动态 Vhd，但删除这些。 动态磁盘附加到它的大小调整虚拟机 (VM) 使处于失败状态的 VM。

  若要解决此问题，请删除 VM，但不删除 VM 的磁盘（存储帐户中的 VHD Blob）。 然后将 VHD 从动态磁盘转换为固定磁盘，然后重新创建虚拟机。

- 当你创建可用性集在门户中通过转到**新建** > **计算** > **可用性集**，你只能创建可用性集的容错域和更新域 1。 一种解决方法，创建新的虚拟机时，创建可用性集使用 PowerShell、 CLI，或在该门户。

- 当 Azure 堆栈用户门户上创建虚拟机时，门户将显示可以将附加到的 DS 系列 VM 的数据磁盘数目不正确。 DS 系列 Vm 可以容纳尽可能多的数据磁盘作为 Azure 配置。

- 当 VM 映像失败时创建时，则可能会将无法删除失败的项添加到 VM 映像计算边栏选项卡中。

  解决方法是，使用可以通过 HYPER-V 创建的 dummy VHD 创建新的 VM 映像 (新建 VHD 的路径 C:\dummy.vhd-固定-SizeBytes 1 GB)。 此过程来解决该问题阻止删除失败的项。 然后，创建该虚拟映像后 15 分钟成功删除它。

  然后，可以尝试重新下载以前失败的 VM 映像。

-  如果预配 VM 部署中的扩展所用时间过长，用户应让而不是尝试停止解除分配或删除 VM 的过程设置超时。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>网络
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
 - 在继续之前，查看中的重要注意事项[在开始之前](#before-you-begin)这些的起点附近发行说明。
- 可能需要最多一小时，才能在用户可以创建新的 SQL 或 MySQL 部署中的数据库。

- 资源提供程序支持在服务器上创建项，该主机 SQL 或 MySQL。 不由资源提供程序创建的项的主机服务器上创建可能会导致不匹配的状态。  


> [!NOTE]  
> 更新到 Azure 堆栈 1802年后，你可以继续使用以前部署的 SQL 和 MySQL 资源提供。  我们建议你更新 SQL 和 MySQL 的新版本可用时。 例如 Azure 堆栈，将更新应用于 SQL 和 MySQL 资源提供程序按顺序。  例如，如果你使用版本 1710年，首先应用版本 1711，然后 1712，，然后更新到 1802年。      
>   
> 更新 1802年安装不影响当前使用的 SQL 或 MySQL 资源提供程序由你的用户。
> 无论你使用的资源提供程序的版本，其数据库中的用户数据不接触，并且仍然可访问。    


#### <a name="app-service"></a>应用服务
- 在订阅中创建其第一个 Azure 函数之前，用户必须注册存储资源提供程序。

- 以横向扩展基础结构 （工作进程、 管理、 前端角色），你必须使用 PowerShell 中所述的发行说明的计算。

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure 堆栈工具
- 使用时*调用 webrequest* PowerShell cmdlet 来下载 Azure 堆栈工具从 Github，您将收到错误：     
    -  *调用 webrequest： 该请求已中止： 无法创建 SSL/TLS 安全通道。*     

  由于新的 GitHub 支持弃用 Tlsv1 和 Tlsv1.1 加密标准 （PowerShell 的默认值） 的发生此错误。 有关详细信息，请参阅[弱加密标准删除通知](https://githubengineering.com/crypto-removal-notice/)。

  若要解决此问题，将添加`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`到要强制 PowerShell 控制台 TLSv1.2 下载时要使用从 GitHub 存储库的脚本的顶部。


## <a name="download-the-update"></a>下载更新
你可以下载 Azure 堆栈 1802年更新包从[此处](https://aka.ms/azurestackupdatedownload)。


## <a name="more-information"></a>详细信息
Microsoft 提供了一种方法来监视和恢复使用特权终结点 (PEP) 与更新 1710年一起安装的更新。

- 请参阅[监视 Azure 堆栈使用特权终结点文档中的更新](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update)。

## <a name="see-also"></a>另请参阅

- 有关 Azure 堆栈中的更新管理的概述，请参阅[管理 Azure 堆栈概述中的更新](azure-stack-updates.md)。
- 有关如何使用 Azure 堆栈应用更新的详细信息，请参阅[在 Azure 堆栈中应用更新](azure-stack-apply-updates.md)。
