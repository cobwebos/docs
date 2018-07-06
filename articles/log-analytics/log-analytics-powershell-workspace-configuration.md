---
title: 使用 PowerShell 创建和配置 Log Analytics 工作区 | Microsoft 文档
description: Log Analytics 使用来自本地或云基础结构中的服务器的数据。 当由 Azure 诊断生成时，可从 Azure 存储收集计算机数据。
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: conceptual
ms.date: 11/21/2016
ms.author: richrund
ms.component: na
ms.openlocfilehash: 375ae9a82af4a1f8a86b529b597ed479388e66dc
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129330"
---
# <a name="manage-log-analytics-using-powershell"></a>使用 PowerShell 管理 Log Analytics
可使用 [Log Analytics PowerShell cmdlet](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) 从命令行或作为脚本的一部分在 Log Analytics 中执行各种函数。  可使用 PowerShell 执行的任务示例包括：

* 创建工作区
* 添加或删除解决方案
* 导入和导出保存的搜索
* 创建计算机组
* 从装有 Windows 代理的计算机启用 IIS 日志收集
* 从 Linux 和 Windows 计算机中收集性能计数器
* 从 Linux 计算机的 syslog 中收集事件 
* 从 Windows 事件日志中收集事件
* 收集自定义事件日志
* 将日志分析代理添加到 Azure 虚拟机
* 将日志分析配置为索引使用 Azure 诊断收集的数据

本文提供两个代码示例，用于演示一些可从 PowerShell 执行的函数。  有关其他函数，可以参阅 [Log Analytics PowerShell cmdlet 参考](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx)。

> [!NOTE]
> Log Analytics 之前称为 Operational Insights，这就是在 cmdlet 中使用此名称的原因。
> 
> 

## <a name="prerequisites"></a>先决条件
这些示例适用于 AzureRm.OperationalInsights 模块 2.3.0 版或更高版本。


## <a name="create-and-configure-a-log-analytics-workspace"></a>创建和配置 Log Analytics 工作区
以下脚本示例演示了如何：

1. 创建工作区
2. 列出可用解决方案
3. 向工作区添加解决方案
4. 导入保存的搜索
5. 导出保存的搜索
6. 创建计算机组
7. 从装有 Windows 代理的计算机启用 IIS 日志收集
8. 从 Linux 计算机中收集逻辑磁盘性能计数器 (% Used Inodes; Free Megabytes; % Used Space; Disk Transfers/sec; Disk Reads/sec; Disk Writes/sec)
9. 从 Linux 计算机中收集 syslog 事件
10. 从 Windows 计算机的应用程序事件日志中收集错误和警告事件
11. 从 Windows 计算机中收集可用内存 (MB) 性能计数器
12. 收集自定义日志 

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>将 Log Analytics 配置为编制 Azure 诊断索引
要对 Azure 资源进行无代理监视，则需要为资源启用 Azure 诊断，并将其配置为写入到 Log Analytics 工作区。 此方法将数据直接发送到 Log Analytics 并且不要求将数据写入到存储帐户。 支持的资源包括：

| 资源类型 | 日志 | 度量值 |
| --- | --- | --- |
| 应用程序网关    | 是 | 是 |
| 自动化帐户     | 是 | |
| 批处理帐户          | 是 | 是 |
| Data Lake Analytics     | 是 | | 
| Data Lake Store         | 是 | |
| SQL 弹性池        |     | 是 |
| 事件中心命名空间     |     | 是 |
| IoT 中心                |     | 是 |
| Key Vault               | 是 | |
| 负载均衡器          | 是 | |
| 逻辑应用              | 是 | 是 |
| 网络安全组 | 是 | |
| Redis 缓存             |     | 是 |
| 搜索服务         | 是 | 是 |
| 服务总线命名空间   |     | 是 |
| SQL (v12)               |     | 是 |
| 网站               |     | 是 |
| Web 服务器场        |     | 是 |

有关可用指标的详细信息，请参阅 [Azure 监视器支持的指标](../monitoring-and-diagnostics/monitoring-supported-metrics.md)。

有关可用日志的详细信息，请参阅[诊断日志支持的服务和架构](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md)。

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

还可以使用前面的 cmdlet 从不同订阅中的资源收集日志。 该 cmdlet 能够跨订阅工作，你同时提供创建日志的资源的 ID 和日志发送到的工作区的 ID。


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>配置 Log Analytics 来为来自存储的 Azure 诊断信息编制索引
要从正在运行的经典云服务或 service fabric 群集实例内收集日志数据，需要首先将数据写入到 Azure 存储。 然后，将 Log Analytics 配置为从存储帐户收集日志。 支持的资源包括：

* 经典云服务（Web 和辅助角色）
* Service Fabric 群集

以下示例介绍如何：

1. 列出 Log Analytics 将为其中的数据编制索引的现有存储帐户和位置
2. 创建配置以从存储帐户读取
3. 更新新创建的配置以为来自其他位置的数据编制索引
4. 删除新创建的配置

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

还可以使用前面的脚本从不同订阅中的存储帐户收集日志。 该脚本能够跨订阅工作，你同时提供存储帐户 ID 和对应的访问密钥。 当更改了访问密钥时，需要更新存储见解以采用新密钥。


## <a name="next-steps"></a>后续步骤
* 有关使用 Log Analytics 的 PowerShell 配置的其他信息，请[查看 Log Analytics PowerShell cmdlet](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx)。

