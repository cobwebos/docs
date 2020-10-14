---
title: '使用 Azure 数据资源管理器 (预览从 Azure Monitor 中查询导出的数据) '
description: 使用 Azure 数据资源管理器可查询从 Log Analytics 工作区导出到 Azure 存储帐户的数据。
ms.subservice: logs
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: ec695912dcd59b474df132cac97d9069da4c5d51
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049659"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 数据资源管理器 (预览从 Azure Monitor 中查询导出的数据) 
将数据从 Azure Monitor 导出到 Azure 存储帐户可以实现低成本保留，并能够将日志重新分配到不同的区域。 使用 Azure 数据资源管理器查询从 Log Analytics 工作区中导出的数据。 配置后，将从你的工作区发送到 Azure 存储帐户的受支持的表将作为 Azure 数据资源管理器的数据源。

流程如下所示： 

1.  将数据从 Log Analytics 工作区导出到 Azure 存储帐户。
2.  在 Azure 数据资源管理器群集中创建外部表，并为数据类型映射。
3.  查询来自 Azure 数据资源管理器的数据。

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Azure 数据资源管理器导出的数据查询流。":::



## <a name="send-data-to-azure-storage"></a>将数据发送到 Azure 存储
可以使用以下任一选项将 Azure Monitor 日志导出到 Azure 存储帐户。

- 若要将 Log Analytics 工作区中的所有数据导出到 Azure 存储帐户或事件中心，请使用 Azure Monitor 日志的 Log Analytics 工作区数据导出功能。 请参阅 [Azure Monitor (预览中 Log Analytics 工作区数据导出) ](logs-data-export.md)
- 使用逻辑应用从日志查询中计划导出。 这类似于数据导出功能，但允许向 Azure 存储发送已筛选或聚合的数据。 尽管此方法受 [日志查询限制的限制](../service-limits.md#log-analytics-workspaces)  ，请参阅 [使用逻辑应用将数据从 Log Analytics 工作区存档到 Azure 存储](logs-export-logic-app.md)。
- 使用逻辑应用进行一次导出。 请参阅 [Azure Monitor 适用于逻辑应用和电源自动的日志连接器](logicapp-flow-connector.md)。
- 使用 PowerShell 脚本一次性导出到本地计算机。 请参阅 [AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport)。

> [!TIP]
> 可以使用现有的 Azure 数据资源管理器群集，或使用所需的配置创建新的专用群集。

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>创建位于 Azure blob 存储中的外部表
使用 [外部表](/azure/data-explorer/kusto/query/schema-entities/externaltables) 将 azure 数据资源管理器链接到 azure 存储帐户。 外部表是引用存储在 Kusto 数据库外部的数据的 Kusto 架构实体。 像表一样，外部表具有定义完善的架构。 与表不同，数据在 Kusto 群集之外进行存储和管理。 之前部分中的导出数据保存在 JSON 行中。

若要创建引用，需要导出表的架构。 使用 Log Analytics 中的 [getschema](/azure/data-explorer/kusto/query/getschemaoperator) 运算符检索包含表的列及其数据类型的信息。

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Azure 数据资源管理器导出的数据查询流。":::

现在可以使用输出创建用于生成外部表的 Kusto 查询。
按照在 [Azure 存储或 Azure Data Lake 中创建和更改外部表](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake)中的指南，以 JSON 格式创建外部表，然后从 Azure 数据资源管理器数据库运行查询。

>[!NOTE]
>外部表的创建是从两个进程生成的。 第一种是创建外部表，第二个是创建映射。

下面的 PowerShell 脚本将创建用于表和映射的 [create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) 命令。

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The log lanlyics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The log lanlyics WorkspaceId
    $WorkspaceName, # The log lanlyics workspace name
    $BlobURL, # The Blob URL where to save
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}

$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'

)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

下图显示了输出的示例。

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="Azure 数据资源管理器导出的数据查询流。":::

[![示例输出](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>复制、粘贴，然后在 Azure 数据资源管理器客户端工具中运行脚本的输出，以创建表和映射。

## <a name="query-the-exported-data-from-azure-data-explorer"></a>查询 Azure 中的导出数据数据资源管理器 

配置映射后，可以从 Azure 数据资源管理器查询导出的数据。 查询需要 [external_table](/azure/data-explorer/kusto/query/externaltablefunction) 函数，如以下示例中所示。

```kusto
external_table("HBTest","map") | take 10000
```

[![查询 Log Analytics 导出的数据](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解如何 [在 Azure 中编写查询数据资源管理器](https://docs.microsoft.com/azure/data-explorer/write-queries)