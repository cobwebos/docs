---
title: Azure 流量分析常见问题解答 | Microsoft Docs
description: 获取有关流量分析的常见问题的解答。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 9f7fb5009468dccae50190ee40a53d11040d0348
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903741"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>流量分析常见问题解答

本文收集了许多有关 Azure 网络观察程序中流量分析的常见问题。

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>使用流量分析的先决条件是什么？

流量分析要求满足以下先决条件：

- 支持网络观察程序的订阅。
- 为要监视的网络安全组 (NSG) 启用 NSG 流日志。
- 存储原始流日志的 Azure 存储帐户。
- 具有读取和写入访问权限的 Azure Log Analytics 工作区。

帐户必须符合以下某项条件才能启用流量分析：

- 必须在订阅级别将帐户分配给以下角色之一：帐户管理员、服务管理员或共同管理员。
- 帐户必须在订阅范围内具有以下任何一种基于角色的访问控制 (RBAC) 角色：所有者、参与者、读者或网络参与者。
- 如果未将帐户分配给之前列出的某一角色，则必须在订阅级别将其分配给分配有以下操作的自定义角色。
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
检查针对订阅分配给用户的角色：

1. 使用 Login-AzureRmAccount 登录 Azure。 

2. 使用 Select-AzureRmSubscription 选择所需订阅。 

3. 若要列出分配给特定用户的所有角色，请使用 Get-AzureRmRoleAssignment -SignInName [用户电子邮件] -IncludeClassicAdministrators。 

如果未看到任何输出，请与相应的订阅管理员联系以获取运行命令的权限。 有关详细信息，请参阅[使用 Azure PowerShell 管理基于角色的访问控制](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-powershell)。


## <a name="in-which-azure-regions-are-traffic-analytics-available"></a>可在哪些 Azure 区域使用流量分析？

可以对以下任何支持区域中的 NSG 使用流量分析：美国中西部、美国东部、美国东部 2、美国中北部、美国中南部、美国中部、美国西部、美国西部 2、西欧、北欧、英国西部、英国南部、澳大利亚东部、澳大利亚东南部和东南亚。 Log Analytics 工作区必须位于美国中西部、美国东部、西欧、英国南部、澳大利亚东南部或东南亚区域。

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>启用流日志的 NSG 是否可与工作区位于不同的区域？

是，这些 NSG 可与 Log Analytics 工作区位于不同区域。

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>是否可以在单个工作区中配置多个 NSG？

是的。

## <a name="can-i-use-an-existing-workspace"></a>是否可以使用现有的的工作区？

是的。 如果选择现有的工作区，请确保已将此工作区迁移到新的查询语言。 如果不想要升级该工作区，则需要创建新的工作区。 有关新查询语言的详细信息，请参阅[将 Azure Log Analytics 升级到新的日志搜索](../log-analytics/log-analytics-log-search-upgrade.md)。

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-operations-management-suite-workspace-be-in-a-different-subscription"></a>是否可将 Azure 存储帐户放在一个订阅中，而将 Operations Management Suite 工作区放在另一个订阅中？

是，可将 Azure 存储帐户置于一个订阅中，而将 Operations Management Suite 工作区置于另一个订阅中。

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>是否可将原始日志存储在不同的订阅中？

不是。 可将原始日志存储在为流日志启用了 NSG 的任何存储帐户中。 但存储帐户和原始日志必须位于同一订阅和区域中。

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>如果由于“未找到”错误而无法为流量分析配置 NSG，该如何解决？

选择支持的区域。 如果选择不支持的区域，则会收到“未找到”错误。 前文列出了支持的区域。

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>如果在 NSG 流日志页显示“无法加载”状态，该如何解决？

要使流日志记录正常工作，必须注册 Microsoft.Insights 提供程序。 如果不确定是否为订阅注册了 Microsoft.Insights 提供程序，请替换以下命令中的 xxxxx-xxxxx-xxxxxx-xxxx，并从 PowerShell 运行以下命令：

```powershell-interactive
**Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>我已配置解决方案。 为何仪表板上未显示任何信息？

首次显示仪表板最长可能需要花费 30 分钟。 解决方案必须先聚合足够的数据以派生有意义的见解。 然后才能生成报告。 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>如果收到此消息：“在选定的时间间隔内无法在此工作区中找到任何数据。 尝试更改时间间隔，或者选择其他工作区”，该如何解决？

请尝试以下选项：
- 在上部菜单栏中更改时间间隔。
- 在上部菜单栏中选择不同的 Log Analytics 工作区。
- 如果流量分析是最近才启用的，请尝试在 30 分钟后访问它。
    
如果问题仍未解决，请在 [User Voice 论坛](https://feedback.azure.com/forums/217313-networking?category_id=195844)中咨询。

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>如果收到此消息：“首次分析 NSG 流日志。 此过程可能需要 20-30 分钟才能完成。 请过一段时间回来查看。 2) 如果上述步骤不起作用，并且工作区位于免费 SKU，则在此处检查工作区使用情况，以验证是否超出配额，或者参阅常见问题解答中的其他信息”，该如何解决？

出现此消息的可能原因有：
- 流量分析最近才启用，可能尚未聚合足够的数据，无法获得有意义的见解。
- 正在使用免费版 Operations Management Suite 工作区，并且它超出了配额限制。 可能需要使用容量更大的工作区。
    
如果问题仍未解决，请在 [User Voice 论坛](https://feedback.azure.com/forums/217313-networking?category_id=195844)中咨询。
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>如果收到此消息：“似乎我们已获得资源数据（拓扑），但没有流信息。 同时，请单击此处查看资源数据，并参阅常见问题解答了解其他信息”，该如何解决？

仪表板上显示了资源信息，但未显示与流相关的统计信息。 由于资源之间没有通信流，因此可能不显示数据。 请在 60 分钟后重新检查状态。 如果问题仍未解决，并且确信资源之间存在通信流，请在 [User Voice 论坛](https://feedback.azure.com/forums/217313-networking?category_id=195844)中咨询。

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>是否可以使用 PowerShell 或 Azure 资源管理器模板或客户端配置流量分析？

可使用版本 6.2.1 及以上版本的 Windows PowerShell 配置流量分析。 若要使用 Set cmdlet 为特定 NSG 配置流日志记录和流量分析，请参阅 [Set-AzureRmNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog?view=azurermps-6.3.0)。 若要获取特定 NSG 的流日志记录和流量分析状态，请参阅 [Get-AzureRmNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkwatcherflowlogstatus?view=azurermps-6.3.0)。

目前，无法使用 Azure 资源管理器模板配置流量分析。

若要使用 Azure 资源管理器客户端配置流量分析，请参阅以下示例。

**Set cmdlet 示例：**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Get cmdlet 示例：**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```



## <a name="how-is-traffic-analytics-priced"></a>流量分析如何计费？

流量分析计量。 该计量的基础是由服务处理流日志数据，并将生成的增强日志存储在 Log Analytics 工作区中。 

例如，根据[定价计划](https://azure.microsoft.com/en-us/pricing/details/network-watcher/)，就美国中西部地区而言，如果流量分析处理的存储帐户中存储的流日志数据为 10 GB，而 Log Analytics 工作区中引入的增强日志为 1 GB 则适用的费用是：10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>如何在地图视图中使用键盘导航？

地图页面包含两个主要部分：
    
- **标题**：地图顶部的标题提供用于选择流量分配筛选器（例如，“部署”、“来自国家/地区的流量”和“恶意”）的按钮。 选择某按钮时，将在地图上应用相应的筛选器。 例如，如果选择“活动”按钮，则地图会突出显示部署中的活动数据中心。
- **地图**：标题下的地图部分显示 Azure 数据中心和国家/地区之间的流量分配。
    
### <a name="keyboard-navigation-on-the-banner"></a>标题中的键盘导航
    
- 地图页面标题部分默认选择“Azure DC”筛选器。
- 若要移至另一个筛选器，请使用 `Tab` 或 `Right arrow` 键。 若要向后移动，请使用 `Shift+Tab` 或 `Left arrow` 键。 向前导航是从左到右，然后是从上到下。
- 按 `Enter` 或 `Down` 箭头键可应用选定的筛选器。 根据选择的筛选器和部署，将在下方的“地图”部分突出显示一个或多个节点。
- 若要在“标题”与“地图”之间切换，请按 `Ctrl+F6`。
        
### <a name="keyboard-navigation-on-the-map"></a>地图中的键盘导航
    
- 在标题中选择任一筛选器并按 `Ctrl+F6` 后，焦点将移至地图视图中某个突出显示的节点（“Azure 数据中心”或“国家/地区”）。
- 若要移至地图中其他突出显示的节点，请使用 `Tab` 或 `Right arrow` 键向前移动。 使用 `Shift+Tab` 或 `Left arrow` 键向后移动。
- 若要在地图中选择突出显示的任一节点，可以使用 `Enter` 或 `Down arrow` 键。
- 选择任一此类节点后，焦点会转移到节点的“信息工具框”。 默认情况下，焦点会转移到“信息工具框”中的关闭按钮。 若要进一步在“框”视图中移动，可分别使用 `Right arrow` 和 `Left arrow` 键向前和向后移动。 按 `Enter` 的效果与在“信息工具框”中选择聚焦的按钮相同。
- 当焦点位于“信息工具框”时，如果按 `Tab`，则焦点会移至选定节点所在的同一大洲中的终结点。 使用 `Right arrow` 和 `Left arrow` 键可浏览这些终结点。
- 若要移至其他流终结点或大洲群集，请使用 `Tab` 向前移动，或使用 `Shift+Tab` 向后移动。
- 焦点位于“大洲群集”时，可以使用 `Enter` 或 `Down` 箭头键突出显示大洲群集中的终结点。 若要在大洲群集的信息框中浏览终结点和使用关闭按钮，可分别使用 `Right arrow` 或 `Left arrow` 键向前或向后移动。 在任一终结点上，可以使用 `Shift+L` 切换到从选定节点到终结点的连接线。 可以再次按 `Shift+L` 以移至所选终结点。
        
### <a name="keyboard-navigation-at-any-stage"></a>任何阶段的键盘导航
    
- 按 `Esc` 可折叠已展开的选定内容。
- 按 `Up arrow` 键可执行按 `Esc` 时所执行的相同操作。 按 `Down arrow` 键可执行按 `Enter` 时所执行的相同操作。
- 使用 `Shift+Plus` 可以放大，使用 `Shift+Minus` 可以缩小。

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>如何在虚拟网络拓扑视图中使用键盘导航？

虚拟网络拓扑页面包含两个主要部分：
    
- **标题**：虚拟网络拓扑顶部标题提供用于选择流量分布筛选器（例如，“已连接的虚拟网络”、“已断开连接的虚拟网络”和“公共 IP”）的按钮。 选择某按钮时，将在拓扑上应用相应的筛选器。 例如，如果选择“活动”按钮，则拓扑会突出显示部署中的活动虚拟网络。
- **拓扑**：标题下的拓扑部分显示虚拟网络之间的流量分布。
    
### <a name="keyboard-navigation-on-the-banner"></a>标题中的键盘导航
    
- 虚拟网络拓扑页面标题部分默认选择“已连接的 VNet”筛选器。
- 若要移至另一个筛选器，请使用 `Tab` 键向前移动。 若要向后移动，请使用 `Shift+Tab` 键。 向前导航是从左到右，然后是从上到下。
- 按 `Enter` 可应用选定的筛选器。 根据选择的筛选器和部署，将在下方的“拓扑”部分突出显示一个或多个节点（虚拟网络）。
- 若要在“标题”与“拓扑”之间切换，请按 `Ctrl+F6`。
        
### <a name="keyboard-navigation-on-the-topology"></a>拓扑中的键盘导航
    
- 在标题中选择任一筛选器并按 `Ctrl+F6` 后，焦点移至拓扑视图中某个突出显示的节点 (VNet)。
- 若要移至拓扑视图中其他突出显示的节点，请使用 `Shift+Right arrow` 键向前移动。 
- 在突出显示的节点上，焦点会移至节点的“信息工具框”。 默认情况下，焦点会移至“信息工具框”中的“更多详细信息”按钮。 若要进一步在“框”视图中移动，可分别使用 `Right arrow` 和 `Left arrow` 键向前和向后移动。 按 `Enter` 的效果与在“信息工具框”中选择聚焦的按钮相同。
- 选择任何此类节点时，可通过按 `Shift+Left arrow` 键逐个访问其所有连接。 焦点将移至该连接的“信息工具框”。 在任何时候，都可通过再次按 `Shift+Right arrow`，将焦点移回该节点。
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>如何在子网拓扑视图中使用键盘导航？

虚拟子网拓扑页面包含两个主要部分：
    
- **标题**：虚拟子网拓扑顶部的标题提供用于选择流量分布筛选器（例如“活动”、“中型”和“网关子网”）的按钮。 选择某按钮时，将在拓扑上应用相应的筛选器。 例如，如果选择“活动”按钮，则拓扑会突出显示部署中的活动虚拟子网。
- **拓扑**：标题下的拓扑部分显示虚拟子网之间的流量分布。
    
### <a name="keyboard-navigation-on-the-banner"></a>标题中的键盘导航
    
- 虚拟子网拓扑页面标题部分默认选择“子网”筛选器。
- 若要移至另一个筛选器，请使用 `Tab` 键向前移动。 若要向后移动，请使用 `Shift+Tab` 键。 向前导航是从左到右，然后是从上到下。
- 按 `Enter` 可应用选定的筛选器。 根据选择的筛选器和部署，将在“拓扑”部分下面突出显示一个或多个节点（子网）。
- 若要在“标题”与“拓扑”之间切换，请按 `Ctrl+F6`。
        
### <a name="keyboard-navigation-on-the-topology"></a>拓扑中的键盘导航
    
- 在标题中选择任一筛选器并按 `Ctrl+F6` 后，焦点移至拓扑视图中某个突出显示的节点（子网）。
- 若要移至拓扑视图中其他突出显示的节点，请使用 `Shift+Right arrow` 键向前移动。 
- 在突出显示的节点上，焦点会移至节点的“信息工具框”。 默认情况下，焦点会移至“信息工具框”中的“更多详细信息”按钮。 若要进一步在“框”视图中移动，可分别使用 `Right arrow` 和 `Left arrow` 键向前和向后移动。 按 `Enter` 的效果与在“信息工具框”中选择聚焦的按钮相同。
- 选择任何此类节点时，可通过按 `Shift+Left arrow` 键逐个访问其所有连接。 焦点将移至该连接的“信息工具框”。 在任何时候，都可通过再次按 `Shift+Right arrow`，将焦点移回该节点。    

