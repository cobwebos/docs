---
title: "使用 Log Analytics 分析 Azure 诊断日志 | Microsoft Docs"
description: "Log Analytics 可以读取将 Azure 诊断日志以 JSON 格式写入 blob 存储的 Azure 服务中的日志。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: adf2f366-ea98-4250-ae66-6d2cfce5b4f9
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 08274c03dd1ebb7533efde4c01744ed5293fb4dd


---
# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>使用 Log Analytics 分析 Azure 诊断日志
Log Analytics 可以为以下将 [Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)以 JSON 格式写入 blob 存储的 Azure 服务收集日志：

* 自动化（预览版）
* 密钥保管库（预览版）
* 应用程序网关（预览版）
* 网络安全组（预览版）

以下部分将引导你使用 PowerShell 来完成：

* 配置 Log Analytics 从每个资源的存储中收集日志  
* 启用 Azure 服务的 Log Analytics 解决方案

必须启用 Azure 诊断之后，Log Analytics 才可以收集这些资源的数据。 使用 `Set-AzureRmDiagnosticSetting` cmdlet 启用日志记录。

有关如何启用诊断日志记录的详细信息，请参阅以下文章：

* [密钥保管库](../key-vault/key-vault-logging.md)
* [应用程序网关](../application-gateway/application-gateway-diagnostics.md)
* [网络安全组](../virtual-network/virtual-network-nsg-manage-log.md)

本文档还包括对以下详细信息：

* 排查数据收集问题
* 停止数据收集

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>配置 Log Analytics 以收集 Azure 诊断日志
为这些服务收集日志并启用解决方案，以直观显示使用 PowerShell 脚本执行的日志。

下面的示例中在所有受支持的资源上启用了日志记录

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


对于某些资源，可以从 Azure 门户中使用 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)所述的步骤执行上述配置。

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>配置 Log Analytics 以收集 Azure 诊断日志
我们提供了可导出两个 cmdlet 的 PowerShell 脚本模块，以帮助配置 Log Analytics：

1. `Add-AzureDiagnosticsToLogAnalyticsUI` 模块会提示你提供输入，并能够设置简单的配置
2. `Add-AzureDiagnosticsToLogAnalytics` 模块会将资源作为输入进行监视，然后配置 Log Analytics  

### <a name="pre-requisites"></a>先决条件
1. 装有 Operational Insights cmdlet 1.0.8 或更高版本的 Azure PowerShell。
   * [如何安装和配置 Azure PowerShell](../powershell-install-configure.md)
   * 验证你的 cmdlets 版本：`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. 针对要监视的 Azure 资源配置诊断日志记录。 有关如何启用诊断的内容，请使用 `Set-AzureRmDiagnosticSetting` 或参考[使用 Log Analytics 收集 Azure 存储帐户中的数据](log-analytics-azure-storage.md)。
3. [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS) 工作区  
4. AzureDiagnosticsAndLogAnalytics PowerShell 模块
   * 从 PowerShell 库下载 [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) 模块

### <a name="option-1-run-the-interactive-configuration-scripts"></a>选项 1：运行交互式配置脚本
打开 PowerShell 并运行：

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

你会看到一个可用选项的列表，并提示你进行选择。
需要对以下各项进行选择：

* 从中收集日志的资源类型（Azure 服务）
* 从中收集日志的资源实例
* 收集数据的 Log Analytics 工作区

运行此脚本，并将新的诊断数据写入存储大约 30 分钟后，你应会在 Log Analytics 中看到记录。 如果经过这段时间后未看到记录，请参阅以下的故障排除部分。

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>选项 2：构建资源列表，并将其传递给配置 cmdlet
你可以构建已启用 Azure 诊断的资源列表，然后将资源传递给配置 cmdlet。

可以通过运行 `Get-Help Add-AzureDiagnosticsToLogAnalytics` 查看有关 cmdlet 的其他信息。

若要查找有关 OMS 的更多详细信息，请参阅 [Log Analytics PowerShell Cmdlet](https://msdn.microsoft.com/library/mt188224.aspx)

> [!NOTE]
> 如果资源和工作区位于不同的 Azure 订阅中，请根据需要使用 `Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>` 在两个订阅之间进行切换
> 
> 

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
运行此脚本，并将新的诊断数据写入存储大约 30 分钟后，你应会在 Log Analytics 中看到记录。 如果经过这段时间后未看到记录，请参阅以下的故障排除部分。  

> [!NOTE]
> 此配置在 Azure 门户中不可见。 你可以使用 `Get-AzureRmOperationalInsightsStorageInsight` cmdlet 验证配置。  
> 
> 

## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>从收集 Azure 诊断日志中停止 Log Analytics
若要删除某个资源的 Log Analytics 配置，请使用 `Remove-AzureRmOperationalInsightsStorageInsight` cmdlet。

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>为 Azure 诊断日志排查配置问题
*问题*

对记录到经典存储的资源进行配置时，会显示以下错误：

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*解决方法*

使用 `Add-AzureAccount` 登录到经典部署模型的 API

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>排查 Azure 诊断日志的数据收集问题
如果未看到 Log Analytics 中的 Azure 资源数据，可以使用以下疑难解答步骤：

* 验证流向存储帐户的数据
* 验证服务的 Log Analytics 解决方案已启用
* 验证 Log Analytics 配置为从存储中读取
* 更新存储帐户密钥

### <a name="verify-data-is-flowing-to-the-storage-account"></a>验证数据流向存储帐户
可以检查数据是否通过允许浏览 Azure 存储（例如 Visual Studio）的工具或通过使用 PowerShell 流向存储帐户。

查找配置到日志的存储帐户资源，以使用以下 PowerShell：

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Azure 诊断使用的存储容器具有一个以下格式的名称：  

`insights-logs-<Category>`

若要了解有关查看存储帐户内容的更多信息，请参阅[使用存储 cmdlet 检查最新数据的容器](../storage/storage-powershell-guide-full.md)。

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>验证服务的 Log Analytics 解决方案已启用
如果使用 `Add-AzureDiagnosticsToLogAnalyticsUI`，则会自动启用正确的 Log Analytics 解决方案。

若要检查某一解决方案是否已启用，可运行以下 PowerShell：

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

如果该解决方案未启用，可以使用以下 PowerShell 启用它：

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

若要找到针对每种资源类型启用的解决方案名称，请使用以下 PowerShell（一旦导入该模块，此变量即为可用）：

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>验证 Log Analytics 配置为从存储中读取
如果添加其他 Azure 资源，则需要为其启用诊断日志记录，并配置 Log Analytics。
若要检查配置 Log Analytics 以收集哪些资源和存储帐户的日志，请使用以下 PowerShell：

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>更新存储密钥
如果更改存储帐户的密钥，则 Log Analytics 配置还需要使用新密钥进行更新。
可以通过以下 PowerShell 使用新密钥更新 Log Analytics 配置：

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

若要查找存储见解名称，可使用 `Get-AzureRmOperationalInsightsStorageInsight` cmdlet，如前面的示例所示。

## <a name="next-steps"></a>后续步骤
* [使用适用于 IIS 的 blob 存储和适用于事件的表存储](log-analytics-azure-storage-iis-table.md)，读取将诊断写入到表存储或将 ISS 日志写入到 blob 存储的 Azure 服务的日志。
* [启用解决方案](log-analytics-add-solutions.md)深入分析数据。
* [使用搜索查询](log-analytics-log-searches.md)分析数据。




<!--HONumber=Nov16_HO3-->


