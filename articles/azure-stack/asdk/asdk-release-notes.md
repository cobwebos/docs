---
title: Microsoft Azure Stack 开发工具包发行说明 |Microsoft Docs
description: Azure Stack 开发工具包的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 12/21/2018
ms.openlocfilehash: c60ba4f4106ddd0c3fc643288894fb55d3d27f8c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816452"
---
# <a name="asdk-release-notes"></a>ASDK 发行说明 
 
本文提供了 Azure Stack 开发工具包 (ASDK) 的改进、修复和已知问题的相关信息。 如果不确定所运行的版本，可以[使用门户检查版本](../azure-stack-updates.md#determine-the-current-version)。

> 请订阅 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，随时了解 ASDK 的新增功能。

## <a name="build-118110101"></a>内部版本 1.1811.0.101

### <a name="new-features"></a>新增功能

此内部版本包含以下适用于 Azure Stack 的改进和修复：  

- ASDK 有一套新的最低硬件和软件要求，以及建议的配置。 [Azure Stack 部署规划注意事项](asdk-deploy-considerations.md)中阐述了这些新的建议规范。 由于 Azure Stack 平台已经演进，现在可以使用更多的服务，此外可能需要更多的资源。 增加的规范反映了这些修订的建议。

### <a name="fixed-issues"></a>修复的问题

<!-- TBD - IS ASDK --> 
- 修复了以下问题：公共 IP 地址使用计量数据针对每条记录显示相同的 **EventDateTime** 值，而不是创建记录时显示的 **TimeDate** 时间戳。 现在，可以使用此数据来执行公共 IP 地址用量的准确计帐。

<!-- 3099544 – IS, ASDK --> 
- 修复了使用 Azure Stack 门户创建新虚拟机 (VM) 时发生的问题。 以前，选择 VM 大小会导致“美元/月”列显示“不可用”消息。 现在，此列不再显示；Azure Stack 不支持显示 VM 定价列。

<!-- 2930718 - IS ASDK --> 
- 修复了以下问题：在管理员门户中访问用户订阅详细信息时，在关闭边栏选项卡并单击“最近”后，用户订阅名称不显示。 现在会显示用户订阅名称。

<!-- 3060156 - IS ASDK --> 
- 修复了管理员门户和用户门户中的以下问题：单击门户设置并选择“删除所有设置和专用仪表板”后，结果不符合预期，同时会显示错误通知。 

<!-- 2930799 - IS ASDK --> 
- 修复了管理员门户和用户门户中的以下问题：在“所有服务”下，资产“DDoS 防护计划”未正确列出，不过，Azure Stack 并不支持该资产。
 
<!--2760466 – IS  ASDK --> 
- 修复了在安装新的 Azure Stack 环境时，不显示指示“需要激活”的警报的问题。 现在会显示该警报。

<!--1236441 – IS  ASDK --> 
- 修复了使用 ADFS 时阻止将 RBAC 策略应用到用户组的问题。

<!--3463840 - IS, ASDK --> 
- 修复了由于无法从公共 VIP 网络访问文件服务器而导致基础结构备份失败的问题。 该项修复会将基础结构备份服务移回公共基础结构网络。 如果应用的最新 Azure Stack 修补程序的 1809年用于解决此问题，1811年更新将不进行任何进一步的修改。 

<!-- 2967387 – IS, ASDK --> 
- 修复了以下问题：登录 Azure Stack 管理员门户或用户门户时使用的帐户显示为“未识别的用户”。 如果帐户中未指定“名字”或“姓氏”，则会显示此消息。   

<!--  2873083 - IS ASDK --> 
- 修复了以下问题：使用门户创建虚拟机规模集 (VMSS) 时，如果使用 Internet Explorer，则“实例大小”下拉列表不会正确加载。 现在，此浏览器可正常工作。  

<!-- 3190553 - IS ASDK -->
- 修复了以下问题：生成的干扰性警报指示某个基础结构角色实例不可用或缩放单元节点已脱机。

### <a name="known-issues"></a>已知问题

#### <a name="portal"></a>门户  

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2931230 – IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 这两种订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- TBD - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- TBD - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

#### <a name="compute"></a>计算 

<!-- 3235634 – IS, ASDK -->
- 若要部署大小包含 **v2** 后缀的 VM（例如 **Standard_A2_v2**），请将后缀指定为 **Standard_A2_v2**（小写 v）。 请勿使用 **Standard_A2_V2**（大写 V）。 这适用于全球 Azure，在 Azure Stack 上有不一致的问题。

<!-- 2869209 – IS, ASDK --> 
- 使用 [**Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0) 时，必须使用 **-OsUri** 参数作为存储帐户 URI（在其中上传磁盘）。 如果使用磁盘的本地路径，则此 cmdlet 会失败并显示以下错误：长时间运行的操作失败，状态为 'Failed'。 

<!--  2795678 – IS, ASDK --> 
- 通过门户创建“高级”VM 大小（DS、Ds_v2、FS、FSv2）的虚拟机 (VM) 时，该 VM 在标准存储帐户中创建。 在标准存储帐户中创建不影响功能、IOPS 或计费。 可以放心忽略带有以下字样的警告：**在 VM 大小需要高级磁盘的情况下，你选择了使用标准磁盘。这可能影响操作系统性能，建议不要这样做。考虑改用高级存储(SSD)。**

<!-- 2967447 - IS, ASDK --> 
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一 OS，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。  

<!-- TBD - IS ASDK --> 
- 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

<!-- 1662991 IS ASDK --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  

<!-- 2724961- IS ASDK --> 
- 在订阅设置中注册 **Microsoft.Insight** 资源提供程序并创建支持来宾 OS 诊断的 Windows VM 时，VM 概述页中的“CPU 百分比”图表不显示指标数据。

   若要查找指标数据，例如 VM 的 CPU 百分比图表，请转到指标窗口并显示所有受支持的 Windows VM 来宾指标。

<!-- 3507629 - IS, ASDK --> 
- 托管磁盘创建了两个新的[计算配额类型](../azure-stack-quota-types.md#compute-quota-types)来限制可以预配的托管磁盘的最大容量。 默认情况下将为每个托管磁盘配额类型分配 2048 GiB。 不过，你可能会遇到以下问题：

   - 对于在 1808 更新之前创建的配额，托管磁盘配额在管理门户中将显示为值 0，虽然分配了 2048 GiB。 你可以根据实际需求增大或减小该值，新设置的配额值将替代 2048 GiB 默认值。
   - 如果将配额值更新为 0，则它等效于默认值 2048 GiB。 作为一种解决方法，请将配额值设置为 1。

<!-- TBD - IS ASDK --> 
- 应用 1811 更新后，在部署包含托管磁盘的 VM 时可能会遇到以下问题：

   1. 如果订阅是在 1808 更新之前创建的，则部署具有托管磁盘的 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 依次单击“资源提供程序”、“Microsoft.Compute”、“重新注册”。
      2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。
   2. 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决错误，请执行[此文章](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步骤来重新配置每个来宾目录。

#### <a name="networking"></a>网络

<!-- 1766332 - IS ASDK --> 
- 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

<!-- 1902460 - IS ASDK --> 
- Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

<!-- 16309153 - IS ASDK --> 
- 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack 机密轮换期间，有一个时段（两到五分钟）会无法访问公共 IP 地址。

<!-- 2664148 - IS ASDK --> 
-   租户通过 S2S VPN 隧道访问其虚拟机时，可能会遇到这样的情况：如果在创建网关后向本地网络网关添加本地子网，连接尝试会失败。 

#### <a name="app-service"></a>应用服务

<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

<!-- #### Usage -->  

<!-- #### Identity -->

## <a name="build-11809090"></a>内部版本 1.1809.0.90

### <a name="new-features"></a>新增功能
此内部版本包含以下适用于 Azure Stack 的改进和修复。  

<!--  2712869   | IS  ASDK -->  
- **Azure Stack syslog 客户端（正式版）** 此客户端允许将与 Azure Stack 基础结构相关的审核、警报和安全日志转发到 Azure Stack 外部的 syslog 服务器或安全信息与事件管理 (SIEM) 软件。 syslog 客户端现在支持指定 syslog 服务器侦听的端口。

此版本意味着 syslog 客户端已正式发布，可以用于生产环境。

有关详细信息，请参阅 [Azure Stack Syslog 转发](../azure-stack-integrate-security.md)。

### <a name="fixed-issues"></a>修复的问题

<!-- TBD - IS ASDK -->
- 在门户中，报告免费/已用容量的内存图表现在是准确的。 可以更可靠地预测可创建的 VM 数量。

<!-- TBD - IS ASDK --> 
- 修复了以下问题：在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- 2702741 -  IS, ASDK --> 修复了在发出“停止-解除分配”命令后，无法保证系统会保留使用动态分配方法部署的公共 IP 的问题。 它们现在已保留。

- <!-- 3078022 - IS, ASDK -->如果 VM 在 1808 之前已停止-解除分配，则该 VM 在 1808 更新之后无法重新分配。  此问题已在 1809 中解决。 处于这种状态且无法启动的实例可以在已应用此修复的 1809 中启动。 此修复还可以防止该问题反复发生。

<!-- TBD -  IS ASDK --> 
- 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以重试下载以前无法下载的 VM 映像。

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**

### <a name="changes"></a>更改

### <a name="known-issues"></a>已知问题

#### <a name="portal"></a>门户  

<!-- 2515955   | IS ,ASDK--> 
- 在 Azure Stack 管理员门户和用户门户中，“所有服务”替换了“更多服务”。 现在可以在 Azure Stack 门户中使用“所有服务”作为替代来导航，就像在 Azure 门户中导航一样。

<!--  TBD – IS, ASDK --> 
- 通过门户[创建虚拟机规模集](../azure-stack-compute-add-scalesets.md) (VMSS) 时，不再使用“基本 A”虚拟机大小。 若要按照此大小来创建 VMSS，请使用 PowerShell 或模板。

#### <a name="health-and-monitoring"></a>运行状况和监视

<!-- 1264761 - IS ASDK -->  
- 可能会看到包含以下详细信息的“运行状况控制器”组件的警报：  

   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描程序不可用。 这可能会影响运行状况报告和指标。

  可以放心忽略这两条警报，它们在一段时间后会自动关闭。  

<!-- 2368581 - IS. ASDK --> 
- 如果 Azure Stack 操作员收到内存不足的警报，并且租户虚拟机无法部署并出现“Fabric VM 创建错误”，则可能表示 Azure Stack 模组的可用内存不足。 请使用 [Azure Stack 容量规划工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)来充分了解可供工作负荷使用的容量。


#### <a name="compute"></a>计算 

<!-- 3164607 – IS, ASDK -->
- 将分离的磁盘重新附加到具有相同名称和 LUN 的同一虚拟机 (VM) 失败，并发生类似于“无法将数据磁盘 'datadisk' 附加到 VM 'vm1'”的错误。 之所以发生该错误，是原因该磁盘目前已分离，或上次分离操作失败。 请等到磁盘完全分离后重试，或再次显式删除/分离磁盘。 解决方法是使用其他名称，或者在其他 LUN 上重新附加磁盘。 

<!-- 3235634 – IS, ASDK -->
- 若要部署大小包含 **v2** 后缀的 VM（例如 **Standard_A2_v2**），请将后缀指定为 **Standard_A2_v2**（小写 v）。 请勿使用 **Standard_A2_V2**（大写 V）。 这适用于全球 Azure，Azure Stack 上的不一致。

<!-- 3099544 – IS, ASDK --> 
- 使用 Azure Stack 门户创建新的虚拟机 (VM) 并选择 VM 大小时，“美元/月”列在显示时会出现“不可用”消息。 此列不应显示；Azure Stack 不支持显示 VM 定价列。

<!-- 2869209 – IS, ASDK --> 
- 使用 [**Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0) 时，必须使用 **-OsUri** 参数作为存储帐户 URI（在其中上传磁盘）。 如果使用磁盘的本地路径，则此 cmdlet 会失败并显示以下错误：长时间运行的操作失败，状态为 'Failed'。 

<!--  2966665 – IS, ASDK --> 
- 将 SSD 数据磁盘附加到高级大小的托管磁盘虚拟机（DS、DSv2、Fs、Fs_V2）失败并出现以下错误：无法更新虚拟机 ‘vmname’ 的磁盘。错误:由于 VM 大小 ‘Standard_DS/Ds_V2/FS/Fs_v2’ 不支持存储帐户类型 ‘Premium_LRS’，因此请求的操作无法执行

   若要解决此问题，请使用 *Standard_LRS* 数据磁盘而不是 *Premium_LRS* 磁盘。 使用 *Standard_LRS* 数据磁盘不会造成 IOPS 或账单费用变化。  

<!--  2795678 – IS, ASDK --> 
- 通过门户创建“高级”VM 大小（DS、Ds_v2、FS、FSv2）的虚拟机 (VM) 时，该 VM 在标准存储帐户中创建。 在标准存储帐户中创建不影响功能、IOPS 或计费。 

   可以放心忽略带有以下字样的警告：*在 VM 大小需要高级磁盘的情况下，你选择了使用标准磁盘。这可能影响操作系统性能，建议不要这样做。考虑改用高级存储(SSD)。*

<!-- 2967447 - IS, ASDK --> 
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一 OS，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。

<!-- TBD -  IS ASDK --> 
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。



<!-- TBD -  IS ASDK --> 
- 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

<!-- 1662991 - IS ASDK --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。

<!-- 2724961- IS ASDK --> 
- 在订阅设置中注册 **Microsoft.Insight** 资源提供程序并创建支持来宾 OS 诊断的 Windows VM 时，VM 概览页中的“CPU 百分比”图表将无法显示指标数据。
 
  若要查找 VM 的“CPU 百分比”图表，请转到“指标”边栏选项卡并查看所有受支持的 Windows VM 来宾指标。

 

#### <a name="networking"></a>网络
<!-- 1766332 - IS, ASDK --> 
- 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

<!-- 1902460 -  IS ASDK --> 
- Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

<!-- 16309153 -  IS ASDK --> 
- 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

<!-- 2702741 -  IS ASDK --> 
- 在发出“停止-解除分配”命令后，无法保证系统会保留使用动态分配方法部署的公共 IP。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack 机密轮换期间，有一个时段（两到五分钟）会无法访问公共 IP 地址。

<!-- 2664148 - IS ASDK --> 
- 租户通过 S2S VPN 隧道访问其虚拟机时，可能会遇到这样的情况：如果在创建网关后向本地网关添加本地子网，连接尝试会失败。 


<!--  #### SQL and MySQL  -->


#### <a name="app-service"></a>应用服务
<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

<!-- TBD - IS ASDK --> 
- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。  


#### <a name="usage"></a>使用情况  
<!-- TBD -  IS ASDK --> 
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!-- #### Identity -->

## <a name="build-11808097"></a>内部版本 1.1808.0.97

### <a name="new-features"></a>新增功能
此内部版本包含以下适用于 Azure Stack 的改进和修复。  

<!-- 1658937 | ASDK, IS --> 
- **按预定义的计划启动备份** - 作为一种设备，Azure Stack 现在可以定期自动触发基础结构备份，不需人为干预。 Azure Stack 还会自动清理那些超出了所定义保留期的备份的外部共享。 有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](../azure-stack-backup-enable-backup-powershell.md)。

<!-- 2496385 | ASDK, IS -->  
- **将数据传输时间加到了总备份时间中。** 有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](../azure-stack-backup-enable-backup-powershell.md)。

<!-- 1702130 | ASDK, IS --> 
- **备份外部容量现在显示正确的外部共享容量。** （以前，此项硬编码成 10 GB。）有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](../azure-stack-backup-enable-backup-powershell.md)。
 
<!-- 2753130 |  IS, ASDK   -->  
- **Azure 资源管理器模板现在支持 condition 元素** - 现在可以使用条件在 Azure 资源管理器模板中部署资源。 可以将模板设计为根据某个条件（例如评估是否存在某个参数值）来部署资源。 若要了解如何使用模板作为条件，请参阅 Azure 文档中的[按条件部署资源](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy)和 [Azure 资源管理器模板的 Variables 节](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables)。 

   也可使用模板[将资源部署到多个订阅或资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment)。  

<!--2753073 | IS, ASDK -->  
- **Microsoft.Network API 资源版本支持已更新**，增加了对 Azure Stack 网络资源 API 版本 2017-10-01（基于 2015-06-15）的支持。 此发行版中未包括对 2015-06-15 和 2017-10-01 之间的资源版本的支持。 有关功能差异，请参阅 [Azure Stack 网络注意事项](../user/azure-stack-network-differences.md)。

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack 增加了对反向 DNS 查找的支持，适用于面向外部的 Azure Stack 基础结构终结点**（即适用于 portal、adminportal、management 和 adminmanagement 终结点）。 这样就可以根据 IP 地址解析 Azure Stack 外部终结点名称。

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack 现在支持向现有 VM 添加其他网络接口。**  可以通过门户、PowerShell 和 CLI 使用此功能。 有关详细信息，请参阅 Azure 文档中的[添加或删除网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm)。 

<!-- 2222444 | IS, ASDK   -->  
- **提高了网络使用情况计量的准确性和复原能力**。 网络使用情况计量现在更准确，考虑到了暂停的订阅、中断期间和争用条件等因素。

<!-- 2297790 | IS, ASDK -->  
- **改进了 Azure Stack Syslog 客户端（预览版功能）**。 此客户端允许将与 Azure Stack 基础结构相关的审核和日志转发到 Azure Stack 外部的 Syslog 服务器或安全信息与事件管理 (SIEM) 软件。 Syslog 客户端现在支持使用纯文本或 TLS 1.2 加密的 TCP 协议，后一种加密为默认配置。 可以使用仅服务器身份验证或相互身份验证对 TLS 连接进行配置。

  若要配置 Syslog 客户端与 Syslog 服务器的通信方式（例如协议、加密和身份验证），请使用 Set-SyslogServer cmdlet。 此 cmdlet 可以在特权终结点 (PEP) 中使用。 

- <!-- ASDK --> **虚拟机规模集的库项现在已内置**。  虚拟机规模集库项现在可以在用户和管理员门户中使用，无需下载。 

- <!-- IS, ASDK --> **虚拟机规模集缩放**。  可以通过门户[缩放虚拟机规模集](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS)。   

- <!-- 2489570 | IS ASDK--> **支持自定义 IPSec/IKE 策略配置**，这适用于 [Azure Stack 中的 VPN 网关](/azure/azure-stack/azure-stack-vpn-gateway-about-vpn-gateways)。

- <!-- | IS ASDK--> **Kubernetes 市场项**。 现在可以使用 [Kubernetes 市场项](/azure/azure-stack/azure-stack-solution-template-kubernetes-cluster-add)来部署 Kubernetes 群集。 用户可以选择 Kubernetes 项并填充一些参数，以便将 Kubernetes 群集部署到 Azure Stack。 模板的用途是方便用户在几个步骤中设置开发/测试型 Kubernetes 部署。

<!-- ####### | IS, ASDK -->  
- **Azure 资源管理器包括区域名称。** 使用此版本时，通过 Azure 资源管理器检索的对象现在将包括区域名称属性。 如果现有 PowerShell 脚本直接将对象传递给另一个 cmdlet，则脚本可能会产生错误并失败。 这是 Azure 资源管理器符合行为，并且要求调用方客户端去除区域属性。 有关 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器文档](https://docs.microsoft.com/azure/azure-resource-manager/)。

<!-- TBD | IS, ASDK -->  
- **在委托的提供商之间移动订阅。** 现在，你可以在属于同一目录租户的新的或现有委托的提供程序订阅之间移动订阅。 属于默认提供商订阅的订阅也可移到同一 Active Directory 租户中的委托提供商订阅。 有关详细信息，请参阅[在 Azure Stack 中委托套餐](../azure-stack-delegated-provider.md)。
 
<!-- 2536808 IS ASDK --> 
- **缩短了 VM 创建时间**：适用于使用从 Azure 市场下载的映像创建的 VM。

### <a name="fixed-issues"></a>修复的问题
- <!-- IS ASDK--> 我们修复了在门户中创建可用性集的问题，该问题导致集只能有 1 个容错域和 1 个更新域。

<!-- TBD | ASDK, IS --> 
- 对更新过程进行了各种改进，使之更可靠。 另外还修复了底层基础结构，这改进了节点清空，因而最大程度地减少了更新过程中可能会造成的工作负荷停机情况。

<!--2292271 | ASDK, IS --> 
- 修复了修改的配额限制无法应用到现有订阅的问题。  现在，如果提高某个网络资源的配额限制，而该资源是与用户订阅关联的套餐和计划的一部分，则新的限制会应用到预先存在的订阅和新的订阅。

<!-- 2448955 | IS ASDK --> 
- 现在可以成功地查询活动日志中是否有在 UTC+N 时区部署的系统。    

<!-- 2319627 |  ASDK, IS --> 
- 对备份配置参数 (Path/Username/Password/Encryption Key) 进行预先检查时，不再为备份配置设置不正确的设置。 （以前会将不正确的设置设置到备份中，然后会造成备份在受到触发的情况下失败。）

<!-- 2215948 |  ASDK, IS --> 
- 备份列表现在会在你手动删除外部共享中的备份时进行刷新。

<!-- 2360715 |  ASDK, IS -->  
- 设置数据中心集成时，不再访问共享中的 AD FS 元数据文件。 有关详细信息，请参阅[通过提供联合元数据文件来设置 AD FS 集成](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)。 

<!-- 2388980 | ASDK, IS --> 
- 修复了一个妨碍用户将此前已分配给某个网络接口或负载均衡器的现有公共 IP 地址分配给新的网络接口或负载均衡器的问题。  

<!-- 2551834 - IS, ASDK --> 
- 在管理员门户或用户门户中选择存储帐户的“概览”时，“概要”窗格现在会正确显示所有预期的信息。 

<!-- 2551834 - IS, ASDK --> 
- 在管理员门户或用户门户中选择存储帐户的“标记”时，信息现在会正确显示。

<!-- TBD - IS ASDK --> 
- 此版 Azure Stack 修复了妨碍 OEM 扩展包中驱动程序更新的应用的问题。

<!-- 2055809- IS ASDK --> 
- 修复了妨碍你在 VM 无法创建时在计算边栏选项卡中删除 VM 的问题。  

<!--  2643962 IS ASDK -->  
- “内存容量不足”警报不再错误显示。

<!--  TBD ASDK --> 
- 承载特权终结点 (PEP) 的虚拟机已增大到 4GB。 在 ASDK 中，此虚拟机名为 AzS-ERCS01。

- <!--  TBD – IS, ASDK --> 通过门户[创建虚拟机规模集](../azure-stack-compute-add-scalesets.md) (VMSS) 时，不再使用“基本 A”虚拟机大小。 若要按照此大小来创建 VMSS，请使用 PowerShell 或模板。 

### <a name="known-issues"></a>已知问题

#### <a name="portal"></a>门户  
- <!-- 2967387 – IS, ASDK --> 登录 Azure Stack 管理员门户或用户门户时使用的帐户显示为“未标识的用户”。 如果帐户未指定“名”或“姓”，则会发生这种情况。 若要解决此问题，请编辑用户帐户，提供名或姓。 然后必须注销，再重新登录门户。 

-  <!--  2873083 - IS ASDK --> 通过门户创建虚拟机规模集 (VMSS) 时，如果使用 Internet Explorer，则“实例大小”下拉列表无法正确加载。 若要解决此问题，请在通过门户创建 VMSS 时使用其他浏览器。  

#### <a name="portal"></a>门户  
<!-- 2931230 – IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!--2760466 – IS  ASDK --> 
- 安装运行此版本的新 Azure Stack 环境时，指示“需要激活”的警报可能不显示。 必须先[激活](../azure-stack-registration.md)，然后才能使用市场联合。 

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 这两种订阅类型为“计量订阅”和“消耗订阅”。 这两种订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- 2403291 - IS ASDK --> 
- 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。
  ![痕迹](media/asdk-release-notes/breadcrumb.png)

<!-- TBD -  IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- TBD -  IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

<!--  TBD | ASDK -->  
- Azure Stack 部署的默认时区现在将设置为 UTC。 在安装 Azure Stack 时，你可以选择时区，但是，在安装期间，它将自动恢复为默认设置 UTC。

#### <a name="health-and-monitoring"></a>运行状况和监视
<!-- 1264761 - IS ASDK -->  
- 可能会看到包含以下详细信息的“运行状况控制器”组件的警报：  

   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描程序不可用。 这可能会影响运行状况报告和指标。

  可以放心忽略这两条警报，它们在一段时间后会自动关闭。  

<!-- 2368581 - IS. ASDK --> 
- 如果 Azure Stack 操作员收到内存不足的警报，并且租户虚拟机无法部署并出现“Fabric VM 创建错误”，则可能表示 Azure Stack 模组的可用内存不足。 请使用 [Azure Stack 容量规划工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)来充分了解可供工作负荷使用的容量。

<!-- TBD - IS. ASDK --> 
- 在特权终结点 (PEP) 上运行 **Test-AzureStack** cmdlet 时，**Azure Stack 基础结构角色实例性能**测试会为 ERCS VM 生成一条警告消息。 可以放心地忽略该警告消息，继续使用 ASDK。

#### <a name="compute"></a>计算
<!-- 2494144 - IS, ASDK --> 
- 选择虚拟机大小进行虚拟机部署时，某些 F 系列 VM 大小在创建 VM 时显示的大小选择器中不可见。 选择器中不显示以下 VM 大小：*F8s_v2*、*F16s_v2*、*F32s_v2* 和 *F64s_v2*。  
  解决方法是，使用下列方法之一部署 VM。 在每种方法中，都需要指定要使用的 VM 大小。

- <!-- 3099544 – IS, ASDK --> 使用 Azure Stack 门户创建新的虚拟机 (VM) 并选择 VM 大小时，“美元/月”列在显示时会出现“不可用”消息。 此列不应显示；Azure Stack 不支持显示 VM 定价列。

- <!-- 2869209 – IS, ASDK --> 使用 [**Add-AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0) 时，必须使用 **-OsUri** 参数作为存储帐户 URI（在其中上传磁盘）。 如果使用磁盘的本地路径，则此 cmdlet 会失败并显示以下错误：长时间运行的操作失败，状态为 'Failed'。 

- <!--  2966665 – IS, ASDK -->将 SSD 数据磁盘附加到高级大小的托管磁盘虚拟机（DS、DSv2、Fs、Fs_V2）失败并出现以下错误：无法更新虚拟机 ‘vmname’ 的磁盘。错误:由于 VM 大小 ‘Standard_DS/Ds_V2/FS/Fs_v2’ 不支持存储帐户类型 ‘Premium_LRS’，因此请求的操作无法执行

   若要解决此问题，请使用 *Standard_LRS* 数据磁盘而不是 *Premium_LRS* 磁盘。 使用 *Standard_LRS* 数据磁盘不会造成 IOPS 或账单费用变化。  

- <!--  2795678 – IS, ASDK --> 通过门户创建“高级”VM 大小（DS、Ds_v2、FS、FSv2）的虚拟机 (VM) 时，该 VM 在标准存储帐户中创建。 在标准存储帐户中创建不影响功能、IOPS 或计费。 

   可以放心忽略带有以下字样的警告：*在 VM 大小需要高级磁盘的情况下，你选择了使用标准磁盘。这可能影响操作系统性能，建议不要这样做。考虑改用高级存储(SSD)。*

- <!-- 2967447 - IS, ASDK --> 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一 OS，或者使用一个 ARM 模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。

<!-- TBD -  IS ASDK --> 
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

<!-- TBD -  IS ASDK --> 
- 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 D 系列 VM 的数据磁盘数不正确。 所有受支持的 D 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

<!-- TBD -  IS ASDK --> 
- 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以重试下载以前无法下载的 VM 映像。

<!-- TBD -  IS ASDK --> 
- 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

<!-- 1662991 - IS ASDK --> 
- Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。

<!-- 2724961- IS ASDK --> 
- 在订阅设置中注册 **Microsoft.Insight** 资源提供程序并创建支持来宾 OS 诊断的 Windows VM 时，VM 概览页不显示指标数据。 

 

#### <a name="networking"></a>网络
<!-- 1766332 - IS, ASDK --> 
- 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

<!-- 1902460 -  IS ASDK --> 
- Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

<!-- 16309153 -  IS ASDK --> 
- 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

<!-- 2702741 -  IS ASDK --> 
- 在发出“停止-解除分配”命令后，无法保证系统会保留使用动态分配方法部署的公共 IP。

<!-- 2529607 - IS ASDK --> 
- 在 Azure Stack 机密轮换期间，有一个时段（两到五分钟）会无法访问公共 IP 地址。

<!-- 2664148 - IS ASDK --> 
- 租户通过 S2S VPN 隧道访问其虚拟机时，可能会遇到这样的情况：如果在创建网关后向本地网关添加本地子网，连接尝试会失败。 


#### <a name="sql-and-mysql"></a>SQL 和 MySQL
<!-- TBD - ASDK --> 
- 托管服务器的数据库必须专用于资源提供程序和用户工作负荷。 不能使用其他任何使用者（包括 应用服务）正在使用的实例。

<!-- IS, ASDK --> 
- 为 SQL 和 MySQL 资源提供程序创建 SKU 时，“系列”名称中不支持使用特殊字符（包括空格和句点）。

#### <a name="app-service"></a>应用服务
<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

<!-- TBD - IS ASDK --> 
- 若要横向扩展基础结构（辅助角色、管理、前端角色），必须按照针对计算的发行说明中的说明来使用 PowerShell。  

<!-- TBD - IS ASDK --> 
- 目前，应用服务只能部署到“默认提供程序订阅”。

#### <a name="usage"></a>使用情况  
<!-- TBD -  IS ASDK --> 
- 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!-- #### Identity -->

