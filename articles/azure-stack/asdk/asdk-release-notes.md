---
title: Microsoft Azure Stack 开发工具包发行说明 |Microsoft Docs
description: Azure Stack 开发工具包的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: 2f732dfdfe9bf4aff2753114c3041f8f646421c2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344726"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 开发工具包发行说明
这些发行说明提供 Azure Stack 开发工具包的改进、修复和已知问题的相关信息。 如果不确定所运行的版本，可以[使用门户检查版本](.\.\azure-stack-updates.md#determine-the-current-version)。

> 请订阅 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，随时了解 ASDK 的新增功能。

## <a name="build-11805142"></a>生成 1.1805.1.42

<!-- ### New features 
This build includes the following improvements and fixes for Azure Stack.  
-->


### <a name="fixed-issues"></a>修复的问题

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**


<!-- ### Changes  --> 


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>已知问题
 
#### <a name="portal"></a>门户
- <!-- TBD - IS ASDK --> 不能将驱动程序更新应用与此版本的 Azure Stack 使用 OEM 扩展包。  没有针对此问题的解决方法。
 
- <!-- TBD - IS ASDK --> 不要使用新的管理订阅类型：“计量订阅”和“消耗订阅”。 这些新订阅类型是在版本 1804 中引入的，目前尚不可用。 请继续使用“默认提供程序”订阅类型。  

- <!-- 2403291 - IS ASDK --> 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。
  ![痕迹](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 删除用户订阅会形成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- <!-- TBD -  IS ASDK --> 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。


#### <a name="health-and-monitoring"></a>运行状况和监视
- <!-- 1264761 - IS ASDK --> 可能会看到具有以下详细信息的*运行状况控制器*组件的警报：  

   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描仪不可用。 这可能会影响运行状况报告和指标。

  可以放心地忽略这两个警报。 它们将随着时间的推移自动关闭。  

#### <a name="compute"></a>计算
- <!-- TBD -  IS ASDK --> 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- <!-- TBD -  IS ASDK --> 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 D 系列 VM 的数据磁盘数不正确。 所有受支持的 D 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- TBD -  IS ASDK --> 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以尝试重新下载以前无法下载的 VM 映像。

- <!-- TBD -  IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 - IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。 

#### <a name="networking"></a>网络
- <!-- 1766332 - IS, ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 2388980 -  IS ASDK --> 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> 如果提高属于某个套餐和计划的网络资源的配额限制，而该套餐和计划与租户订阅相关联，则新的限制不会应用到该订阅。 但是，新限制会应用到在配额提高后创建的新订阅。 

  当计划已与某个订阅相关联时，若要解决此问题，请使用加载项计划来增大网络配额。 有关详细信息，请参阅如何[提供加载项计划](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

- <!-- 2304134 IS ASDK --> 不能删除与 DNS 区域资源或路由表资源相关联的订阅。 若要成功地删除该订阅，必须先从租户订阅中删除 DNS 区域资源和路由表资源。 


- <!-- 1902460 -  IS ASDK --> Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- <!-- 16309153 -  IS ASDK --> 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。
 
- <!-- TBD -  IS ASDK --> Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。


#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- <!-- TBD - ASDK --> 托管服务器的数据库必须专用于资源提供程序和用户工作负荷。 不能使用其他任何使用者（包括 应用服务）正在使用的实例。

- <!-- IS, ASDK --> 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**名称中不支持使用特殊字符（包括空格和句点）。 

#### <a name="app-service"></a>应用服务
- <!-- 2352906 - IS ASDK --> 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- <!-- TBD - IS ASDK --> 若要横向扩展基础结构（辅助角色、管理角色、前端角色），必须按照计算发行说明中的说明来使用 PowerShell。  

- <!-- TBD - IS ASDK --> 目前，应用服务只能部署到“默认提供程序订阅”。 在将来的更新中，应用服务将部署到 Azure Stack 1804 中引入的新“计量订阅”。 支持使用“计量”时，现有的所有部署将迁移到此新订阅类型。

#### <a name="usage"></a>使用情况  
- <!-- TBD -  IS ASDK --> 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!-- #### Identity -->



## <a name="build-201805131"></a>内部版本 20180513.1

### <a name="new-features"></a>新增功能 
此内部版本包含以下适用于 Azure Stack 的改进和修复。  

- <!-- 1759172 - IS, ASDK --> **新的管理订阅**。 应用更新 1804 后，门户中会显示两个新的订阅类型。 这些新订阅类型是对“默认提供程序”订阅的补充，从版本 1804 开始，会显示在新的 Azure Stack 安装中。 请不要在此 Azure Stack 版本中使用这些新订阅类型。 如果这些订阅将来可用，我们将在相应的更新中发布通告。 

  这些新订阅类型会显示，但它们是某项重大更改的一部分，旨在保护“默认提供程序”订阅，以及方便部署 SQL 宿主服务器等共享资源。 

  目前提供的三个订阅类型是：  
  - “默认提供程序”订阅：请继续使用此订阅类型。 
  - “计量”订阅：请不要继续使用此订阅类型。
  - “消耗量”订阅：请不要继续使用此订阅类型。

### <a name="fixed-issues"></a>修复的问题
- <!-- IS, ASDK -->  在管理员门户中，不再需要刷新“更新”磁贴才能显示信息。 

- <!-- 2050709 - IS, ASDK -->  现在可以使用管理员门户来编辑 Blob 服务、表服务和队列服务的存储指标。

- <!-- IS, ASDK --> 在“网络”下单击“连接”以设置 VPN 连接时，“站点到站点(IPsec)”现在是唯一可用的选项。 

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**

<!-- ### Changes  --> 
### <a name="additional-releases-timed-with-this-update"></a>随此更新一起推出的其他发行版  
以下版本现在可用，但不需要 Azure Stack 更新 1804。
- **更新到 Microsoft Azure Stack 的 System Center Operations Manager 监视包**。 适用于 Azure Stack 的 Microsoft System Center Operations Manager 监视包的新版本 (1.0.3.0) 已可供[下载](https://www.microsoft.com/download/details.aspx?id=55184)。 使用此版本，可以在添加连接的 Azure Stack 部署时使用服务主体。 此版本还提供更新管理体验，可让你直接从 Operations Manager 中执行修正操作。 还有新的仪表板，用于显示资源提供程序、缩放单元和缩放单元节点。

- **新的 Azure Stack 管理 PowerShell 版本 1.3.0**。  Azure Stack PowerShell 1.3.0 现已可供安装。 此版本针对所有管理资源提供程序提供了用来管理 Azure Stack 的命令。  在此版本中，某些内容将从 Azure Stack 工具 GitHub [存储库](https://github.com/Azure/AzureStack-Tools)中弃用。 

   有关安装的详细信息，请遵循 Azure Stack 模块 1.3.0 的[说明](.\.\azure-stack-powershell-install.md)或[帮助](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)内容。 

- **Azure Stack API Rest 参考的初始版本**。 [所有 Azure Stack 管理资源提供程序的 API 参考](https://docs.microsoft.com/rest/api/azure-stack/)现已发布。 

### <a name="known-issues"></a>已知问题
 
#### <a name="portal"></a>门户
- <!-- TBD - IS ASDK --> 不能将驱动程序更新应用与此版本的 Azure Stack 使用 OEM 扩展包。  没有针对此问题的解决方法。
 
- <!-- TBD - IS ASDK --> 在管理员门户中[从下拉列表提交新的支持请求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)的功能不可用。 请改用以下链接：     
    - 对于 Azure Stack 开发工具包，请使用 https://aka.ms/azurestackforum。    

- <!-- 2403291 - IS ASDK --> 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。
  ![痕迹](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 删除用户订阅会形成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- <!-- TBD -  IS ASDK --> 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

-   <!-- TBD -  IS ASDK --> 在管理员门户中，可能会看到针对 Microsoft.Update.Admin 组件的严重警报。 警报名称、说明和修正均显示为：  
    - 错误 - FaultType 为 ResourceProviderTimeout 的模板缺失。

    可以放心地忽略此警报。 

#### <a name="health-and-monitoring"></a>运行状况和监视
- <!-- 1264761 - IS ASDK --> 可能会看到具有以下详细信息的*运行状况控制器*组件的警报：  

   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描仪不可用。 这可能会影响运行状况报告和指标。

  可以放心地忽略这两个警报。 它们将随着时间的推移自动关闭。  

#### <a name="marketplace"></a>市场
- 用户无需订阅就能浏览整个市场，并且将会看到计划和套餐等管理项。 对用户而言，这些项是非功能性的。
 
#### <a name="compute"></a>计算
- <!-- TBD -  IS ASDK --> 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- <!-- TBD -  IS ASDK --> 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- TBD -  IS ASDK --> 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以尝试重新下载以前无法下载的 VM 映像。

- <!-- TBD -  IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 - IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。 

#### <a name="networking"></a>网络
- <!-- 1766332 - IS, ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 2388980 -  IS ASDK --> 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> 如果提高属于某个套餐和计划的网络资源的配额限制，而该套餐和计划与租户订阅相关联，则新的限制不会应用到该订阅。 但是，新限制会应用到在配额提高后创建的新订阅。 

  当计划已与某个订阅相关联时，若要解决此问题，请使用加载项计划来增大网络配额。 有关详细信息，请参阅如何[提供加载项计划](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

- <!-- 2304134 IS ASDK --> 不能删除与 DNS 区域资源或路由表资源相关联的订阅。 若要成功地删除该订阅，必须先从租户订阅中删除 DNS 区域资源和路由表资源。 


- <!-- 1902460 -  IS ASDK --> Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- <!-- 16309153 -  IS ASDK --> 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。
 
- <!-- TBD -  IS ASDK --> Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。


#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- <!-- TBD - ASDK --> 托管服务器的数据库必须专用于资源提供程序和用户工作负荷。 不能使用其他任何使用者（包括 应用服务）正在使用的实例。

- <!-- IS, ASDK --> 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**名称中不支持使用特殊字符（包括空格和句点）。 

#### <a name="app-service"></a>应用服务
- <!-- TBD -  IS ASDK --> 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- <!-- TBD -  IS ASDK --> 若要横向扩展基础结构（辅助角色、管理角色、前端角色），必须按照计算发行说明中的说明来使用 PowerShell。
 
#### <a name="usage"></a>使用情况  
- <!-- TBD -  IS ASDK --> 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell cmdlet 从 Github 下载 Azure Stack 工具时，收到一个错误：     
    -  invoke-webrequest: 请求已终止: 未能创建 SSL/TLS 安全通道。     

  之所以发生此错误，是因为最近的 GitHub 支持弃用了 Tlsv1 和 Tlsv1.1 加密标准（PowerShell 的默认设置）。 有关详细信息，请参阅 [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/)（弱加密标准删除通知）。

<!-- #### Identity -->



## <a name="build-201803021"></a>内部版本 20180302.1

### <a name="new-features-and-fixes"></a>新功能和修复
为 Azure Stack 集成系统版本 1803 发布的新功能和修复将应用到 Azure Stack 开发工具包。 请参阅 Azure Stack 1803 更新发行说明的[新功能](.\.\azure-stack-update-1803.md#new-features)和[修复的问题](.\.\azure-stack-update-1803.md#fixed-issues)部分以了解详细信息。  
> [!IMPORTANT]    
> **新功能**和**修复的问题**部分所列的某些项仅与 Azure Stack 集成系统相关。

### <a name="changes"></a>更改
- 将新创建的产品/服务的状态从“专用”更改为“公用”或“停止使用”的方式已变。 有关详细信息，请参阅[创建套餐](.\.\azure-stack-create-offer.md)。 


### <a name="known-issues"></a>已知问题
 
#### <a name="portal"></a>门户
- 在管理员门户中[从下拉列表提交新的支持请求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)的功能不可用。 请改用以下链接：     
    - 对于 Azure Stack 开发工具包，请使用 https://aka.ms/azurestackforum。    

- <!-- 2050709 --> 在管理员门户中，不能编辑 Blob 服务、表服务或队列服务的存储指标。 转到“存储”并选择 Blob、表或队列服务磁贴后，就会打开一个新的边栏选项卡，其中显示该 服务的指标图表。 如果随后从指标图表磁贴顶部选择“编辑”，则会打开“编辑图表”边栏选项卡，但其中不显示用于编辑指标的选项。  

- 看到“需要激活”警告警报，提示注册 Azure Stack 开发工具包。 这是预期的行为。

- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

- 在管理门户的仪表板中，“更新”磁贴无法显示有关更新的信息。 若要解决此问题，请单击该磁贴对其进行刷新。

-   在管理门户中，可能会看到针对 Microsoft.Update.Admin 组件的严重警报。 警报名称、说明和修正均显示为：  
    - 错误 - FaultType 为 ResourceProviderTimeout 的模板缺失。

    可以放心地忽略此警报。 

- 在管理员门户和用户门户中，如果选择通过旧版 API（例如 2015-06-15）创建的存储帐户的“概述”边栏选项卡，则“概述”边栏选项卡无法加载。 

  解决方法是使用 PowerShell 运行 **Start-ResourceSynchronization.ps1** 脚本，以便重新可以访问存储帐户详细信息。 [GitHub 中提供了该脚本]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)。如果使用 ASDK，则必须在开发工具包主机上使用服务管理员凭据运行该脚本。  

- “服务运行状况”边栏选项卡无法加载。 在管理员或用户门户中打开“服务运行状况”边栏选项卡时，Azure Stack 显示错误且不加载信息。 这是预期的行为。 尽管可以选择并打开“服务运行状况”，但此功能目前不可用，将来的 Azure Stack 版本中会实现此功能。


#### <a name="health-and-monitoring"></a>运行状况和监视
- <!-- 1264761 - IS ASDK --> 可能会看到具有以下详细信息的*运行状况控制器*组件的警报：  

   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描仪不可用。 这可能会影响运行状况报告和指标。

  可以放心地忽略这两个警报。 它们将随着时间的推移自动关闭。  

- 在 Azure Stack 管理员门户中，可能会显示名称为“外部证书即将过期”的严重警报。  可以放心忽略此警报，它不会影响 Azure Stack 开发工具包的运行。 


#### <a name="marketplace"></a>市场
- 用户无需订阅就能浏览整个市场，并且将会看到计划和套餐等管理项。 对用户而言，这些项是非功能性的。
 
#### <a name="compute"></a>计算
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以尝试重新下载以前无法下载的 VM 映像。

-  如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。 


#### <a name="networking"></a>网络
- 如果在“网络”下单击“连接”来设置 VPN 连接，会列出“VNet 到 VNet”作为可能的连接类型。 请不要选择此选项。 目前，仅支持“站点到站点(IPsec)”选项。

- 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。



- Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。
 
- Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。



#### <a name="sql-and-mysql"></a>SQL 和 MySQL 
- 可能需要在长达一小时的时间过后，用户才能在新的 SQL 或 MySQL SKU 中创建数据库。

- 托管服务器的数据库必须专用于资源提供程序和用户工作负荷。 不能使用其他任何使用者（包括 应用服务）正在使用的实例。

- <!-- IS, ASDK --> 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**或**层级**名称中不支持使用特殊字符（包括空格和句点）。

#### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。
 
#### <a name="usage"></a>使用情况  
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。
<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell cmdlet 从 Github 下载 Azure Stack 工具时，收到一个错误：     
    -  invoke-webrequest: 请求已终止: 未能创建 SSL/TLS 安全通道。     

  之所以发生此错误，是因为最近的 GitHub 支持弃用了 Tlsv1 和 Tlsv1.1 加密标准（PowerShell 的默认设置）。 有关详细信息，请参阅 [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/)（弱加密标准删除通知）。

  若要解决此问题，请将 `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` 添加到脚本顶部，强制 PowerShell 控制台在从 GitHub 存储库下载项目时使用 TLSv1.2。
