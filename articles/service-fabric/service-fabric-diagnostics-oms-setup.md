---
title: Azure Service Fabric - 使用 Log Analytics 设置监视 | Microsoft Docs
description: 了解如何设置 Log Analytics 来可视化和分析事件，进而监视 Azure Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 2cdc9f780e0d9ddb6e688e53208e5597b000aa56
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632766"
---
# <a name="set-up-log-analytics-for-a-cluster"></a>为群集设置 Log Analytics

若要监视群集级别的事件，建议使用 Log Analytics。 可以通过 Azure 资源管理器、PowerShell 或 Azure 市场设置 Log Analytics 工作区。 对于所做的部署，如果要保留更新的资源管理器模板供将来使用，请使用同一模板设置 Log Analytics 环境。 如果部署的群集已启用诊断，则通过市场部署更方便。 如果你在要部署到的帐户中没有订阅级别访问权限，请通过 PowerShell 或资源管理器模板进行部署。

> [!NOTE]
> 要设置 Log Analytics 来监视群集，需要启用诊断功能以监视群集级别或平台级别事件。 有关详细信息，请参阅[如何在 Windows 群集中设置诊断](service-fabric-diagnostics-event-aggregation-wad.md)和[如何在 Linux 群集中设置诊断](service-fabric-diagnostics-event-aggregation-lad.md)

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>使用 Azure 市场部署 Log Analytics 工作区

若要在部署群集之后添加 Log Analytics 工作区，请转到门户中的 Azure 市场，然后查找“Service Fabric 分析”。 这是一个适用于 Service Fabric 部署的自定义解决方案，其数据是特定于 Service Fabric 的。 在此过程中，需创建解决方案（用于查看见解的仪表板）和工作区（聚合了基础的群集数据）。

1. 在左侧导航菜单中选择“新建”。 

2. 搜索“Service Fabric 分析”。 选择显示的资源。

3. 选择“创建”。

    ![市场中的 Service Fabric 分析](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. 在 Service Fabric 分析创建窗口中，为“OMS 工作区”字段选中“选择工作区”，然后选择“创建新工作区”。 填写所需的条目。 此处仅要求 Service Fabric 群集和工作区的订阅相同。 验证条目后，即开始部署工作区。 几分钟即可完成部署。

5. 完成后，在 Service Fabric 分析创建窗口底部再次选择“创建”。 请确保新工作区显示在 OMS 工作区下。 此操作会将解决方案添加到所创建的工作区中。

如果使用的是 Windows，请继续执行以下步骤，将 Log Analytics 连接到存储群集事件的存储帐户。 

>[!NOTE]
>尚不可对 Linux 群集启用此体验。 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>将 Log Analytics 工作区连接到群集 

1. 需要将工作区连接到来自群集的诊断数据。 转到在其中创建 Service Fabric 分析解决方案的资源组。 选择 **ServiceFabric\<nameOfWorkspace\>**，然后转到其概述页。 可在此处更改解决方案设置和工作区设置，还可访问 Log Analytics 工作区。

2. 在左侧导航菜单的“工作区数据源”下，选择“存储帐户日志”。

3. 在“存储帐户日志”页面顶部选择“添加”，将群集的日志添加到工作区。

4. 选择“存储帐户”，添加在群集中创建的相应帐户。 若使用的是默认名称，存储帐户为 sfdg\<resourceGroupName\>。 还可检查 applicationDiagnosticsStorageAccountName 所使用的值，进而通过部署群集时所用的 Azure 资源管理器模板确认此名称。 如果不显示名称，请向下滚动并选择“加载更多”。 选择存储帐户名称。

5. 指定数据类型。 将其设置为“Service Fabric 事件”。

6. 确保源已自动设置为 WADServiceFabric\*EventTable。

7. 选择“确定”，将工作区连接到群集的日志。

    ![将存储帐户日志添加到 Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

该帐户现显示为工作区数据源中存储帐户日志的一部分。

已将 Service Fabric 分析解决方案添加到 Log Analytics 工作区中，后者现已正确连接到群集的平台和应用程序日志表。 可以用相同的方式将其他源添加到工作区中。


## <a name="deploy-log-analytics-with-azure-resource-manager"></a>使用 Azure 资源管理器模板部署 Log Analytics

使用资源管理器模板部署群集时，该模板还会新建一个 Log Analytics 工作区，向其添加 Service Fabric 解决方案，并将其配置为读取相应存储表中的数据。

可使用[此示例模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure)，还可进行修改，使其满足你的要求。 此模板执行以下操作

* 创建一个 5 节点 Service Fabric 群集
* 创建 Log Analytics 工作区和 Service Fabric 解决方案
* 配置 Log Analytics 代理以收集 2 个示例性能计数器并将其发送到工作区
* 配置 WAD 以收集 Service Fabric 并将其发送到 Azure 存储表 (WADServiceFabric*EventTable)
* 配置 Log Analytics 工作区以从这些表中读取事件


可以在 AzureRM PowerShell 模板中使用 `New-AzureRmResourceGroupDeployment` 将模板部署为资源管理器升级到群集。 示例命令如下：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure 资源管理器可检测出此命令要更新到现有资源。 它仅处理驱动现有部署的模板和提供的新模板之间的变化。

## <a name="deploy-log-analytics-with-azure-powershell"></a>使用 Azure PowerShell 部署 Log Analytics

还可使用 `New-AzureRmOperationalInsightsWorkspace` 命令通过 PowerShell 部署 Log Analytics 资源。 要使用此方法，请确保已安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1)。 使用此脚本新建 Log Analytics 工作区，并向其添加 Service Fabric 解决方案： 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

完成后，请按照上一部分中的步骤将 Log Analytics 连接到相应的存储帐户。

还可添加其他解决方案或使用 PowerShell 对 Log Analytics 工作区进行其他修改。 有关详细信息，请参阅 [使用 PowerShell 管理 Log Analytics](../log-analytics/log-analytics-powershell-workspace-configuration.md)。

## <a name="next-steps"></a>后续步骤
* [将 Log Analytics 代理部署到节点上](service-fabric-diagnostics-oms-agent.md)，以收集性能计数器、docker 统计信息和容器日志
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
* [使用视图设计器在 Log Analytics 中创建自定义视图](../azure-monitor/platform/view-designer.md)
