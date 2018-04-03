---
title: Azure 堆栈 1803年更新 |Microsoft 文档
description: 了解什么是 Azure 堆栈 1803年更新中集成的系统、 已知的问题和下载更新的位置。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 560261b5d353c7e9510124985c644d895612e2bb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1803-update"></a>Azure 堆栈 1803年更新

*适用于：Azure Stack 集成系统*

本文介绍改进和 1803年更新包的已知问题有关此版本中，以及在何处下载此更新中修复。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]        
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考    
Azure 堆栈 1803年更新生成号是**20180329.1**。


## <a name="before-you-begin"></a>开始之前    
> [!IMPORTANT]    
> 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](/azure-stack-updates#plan-for-updates)。


### <a name="prerequisites"></a>必备组件
- 安装 Azure 堆栈[1802年更新](azure-stack-update-1802.md)应用 Azure 堆栈 1803年更新前。    


### <a name="post-update-steps"></a>更新后步骤
*有更新 1803年无更新后的步骤。*


### <a name="new-features"></a>新增功能 
此更新包含以下适用于 Azure Stack 的改进和修复。

- **更新 Azure 堆栈机密**-（帐户和证书）。 有关管理机密的详细信息，请参阅[旋转 Azure 堆栈中的机密](azure-stack-rotate-secrets.md)。 

- <!-- 1914853 --> **Automatic redirect to HTTPS** when you use HTTP to access the administrator and user portals. This improvement was made based on [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) feedback for Azure Stack. 

- <!-- 2202621  --> **Access the Marketplace** – You can now open the Azure Stack Marketplace by using the [+New](https://ms.portal.azure.com/#create/hub) option from within the admin and user portals the same way you do in the Azure portals.
 
- <!-- 2202621 --> **Azure Monitor** - Azure Stack adds [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) to the admin and user portals. This includes new explorers for metrics and activity logs. To access this Azure Monitor from external networks, port **13012** must be open in firewall configurations. For more information about ports required by Azure Stack, see [Azure Stack datacenter integration - Publish endpoints](azure-stack-integrate-endpoints.md).

   也可用作此过程中更改下,**更多的服务**，*审核日志*现在显示为*活动日志*。 功能现已与 Azure 门户一致。 

- <!-- 1664791 --> **Sparse files** -  When you add a New image to Azure Stack, or add an image through marketplace syndication, the image is converted to a sparse file. Images that were added prior to using Azure Stack version 1803 cannot be converted. Instead, you must use marketplace syndication to resubmit those images to take advantage of this feature. 
 
   稀疏文件是用于减少存储空间使用和改进 I/O 高效的文件格式。  有关详细信息，请参阅[Fsutil 稀疏](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse)for Windows Server。 


### <a name="fixed-issues"></a>修复的问题

- <!-- 1739988 -->  **Fixed** - Internal Load Balancing (ILB) now properly handles MAC addresses for back-end VMs, which causes ILB to drop packets to the back-end network when using Linux instances on the back-end network. ILB works fine with Windows instances on the back-end network. 

- <!-- 1805496 --> **Fixed** - An issue where VPN Connections between Azure Stack would become disconnected due to Azure Stack using different settings for the IKE policy than Azure.  The values now match the values in Azure. 

- <!-- 2209262 --> **Fixed** - The IP issue where VPN Connections was previously visible in the portal; however enabling or toggling IP Forwarding has no effect. The feature is turned on by default and the ability to change this not yet supported.  The control has been removed from the portal. 

- <!-- 1766332 --> **Fixed** - Azure Stack does not support Policy Based VPN Gateways, even though the option appears in the Portal.  The option has been removed from the Portal. 

- <!-- 2096388 --> **Fixed** - Unable to update Network Security Group Rules from the Portal is now fixed. 

- <!-- 1868283 --> **Fixed** - Azure Stack now prevents resizing of a virtual machine that is created with dynamic disks. 

- <!-- 1756324--> **Fixed** - Usage data for virtual machines is now separated at hourly intervals. This is consistent with Azure. 

- <!--  2253274 --> **Fixed** - The issue where in the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and manage this information.

- **固定**-当你创建虚拟机时，消息*无法显示定价*不会再出现在选择 VM 大小的大小。

- **各种修复**的性能、 稳定性、 安全性和使用由 Azure 堆栈的操作系统。


### <a name="changes"></a>更改
- 从新创建的产品/服务的状态更改的方式*私有*到*公共*或*已解除授权*已更改。 有关详细信息，请参阅[创建提议](azure-stack-create-offer.md)。


### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题    
*没有为在安装更新 1803年已知的问题。*


### <a name="known-issues-post-installation"></a>已知问题（安装后）
以下是安装后的生成的已知的问题**20180323.2**。

#### <a name="portal"></a>门户
- 在管理员门户中[从下拉列表提交新的支持请求](azure-stack-manage-portals.md#quick-access-to-help-and-support)的功能不可用。 请改用以下链接：     
    - 对于 Azure Stack 集成系统，请使用 https://aka.ms/newsupportrequest。

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- 无法在管理员门户中查看计算或存储资源。 此问题的原因是更新安装过程中出错，导致系统错误地将更新报告为成功。 如果发生此问题，请联系 Microsoft 客户支持服务部门以寻求帮助。

- 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。

- 查看资源或资源组的属性时，发现“移动”按钮已禁用。 这是预期的行为。 目前不支持在资源组或订阅之间移动资源或资源组。

- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

- 在管理门户的仪表板中，“更新”磁贴无法显示有关更新的信息。 若要解决此问题，请单击该磁贴对其进行刷新。

- 在管理门户中，你可能会看到的严重警报*Microsoft.Update.Admin*组件。 警报名称、说明和修正均显示为：  
    - 错误 - FaultType 为 ResourceProviderTimeout 的模板缺失。

  可以放心地忽略此警报。 


<!-- #### Health and monitoring --> 

#### <a name="marketplace"></a>Marketplace
- 用户无需订阅就能浏览整个商城，并且能看到计划和产品/服务等管理项。 对用户而言，这些项是非功能性的。



#### <a name="compute"></a>计算
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- <!-- 2290877  --> You cannot scale up a virtual machine scale set (VMSS) that was created when using Azure Stack prior to version 1803. This is due to the change in support for using availability sets with virtual machine scale sets. This support is added with version 1803.  When you attempt to add additional instances to scale a VMSS that was created prior to this support being added, the action fails with the message Provisioning state failed. We are investigating a fix for this issue to enable an older VMSS to scale, and will update this content if and when that is available. 

- 通过转到“新建” > “计算” > “可用性集”在门户中创建可用性集时，只能创建一个包含一个容错域和 1 个更新域的可用性集。 解决方法是在创建新的虚拟机时，通过 PowerShell、CLI 或门户来创建可用性集。

- 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以尝试重新下载以前无法下载的 VM 映像。

-  如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  


#### <a name="networking"></a>网络
- 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。



- Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

- Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。


#### <a name="sql-and-mysql"></a>SQL 和 MySQL
- 在继续操作之前，请查看这些发行说明开头附近的[开始之前](#before-you-begin)中的重要说明。

- 可能需要在长达一小时的时间过后，用户才能在新的 SQL 或 MySQL 部署中创建数据库。

- 只有资源提供程序才能在托管 SQL 或 MySQL 的服务器上创建项目。 如果在不是由资源提供程序创建的主机服务器上创建项目，则此类项目可能导致状态不匹配。  


> [!NOTE]  
> 更新到 Azure 堆栈 1803年后，你可以继续使用以前部署的 SQL 和 MySQL 资源提供。  建议在新版本发布后更新 SQL 和 MySQL。 与 Azure Stack 一样，请将更新按顺序应用到 SQL 和 MySQL 资源提供程序。  例如，如果你使用版本 1711年，首先应用版本 1712，然后 1802，，然后更新到 1803年。      
>   
> 更新 1803年安装不影响当前使用的 SQL 或 MySQL 资源提供程序由你的用户。
> 不管所用资源提供程序的版本如何，在其数据库中的用户数据不会受到影响，仍然可用。    



#### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。


#### <a name="usage"></a>使用情况  
- 使用公共 IP 地址使用情况计数数据演示如何将相同*EventDateTime*而不是每个记录的值*TimeDate*显示时已创建了记录的 stamp。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell cmdlet 从 Github 下载 Azure Stack 工具时，收到一个错误：     
    -  invoke-webrequest: 请求已终止: 未能创建 SSL/TLS 安全通道。     

  之所以发生此错误，是因为最近的 GitHub 支持弃用了 Tlsv1 和 Tlsv1.1 加密标准（PowerShell 的默认设置）。 有关详细信息，请参阅 [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/)（弱加密标准删除通知）。

  若要解决此问题，请将 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 添加到脚本顶部，强制 PowerShell 控制台在从 GitHub 存储库下载项目时使用 TLSv1.2。


## <a name="download-the-update"></a>下载更新
你可以下载 Azure 堆栈 1803年更新包从[此处](https://aka.ms/azurestackupdatedownload)。


## <a name="see-also"></a>另请参阅
- 若要使用特权终结点 (PEP) 监视和恢复更新，请参阅[监视 Azure 堆栈使用特权终结点中的更新](azure-stack-monitor-update.md)。
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
