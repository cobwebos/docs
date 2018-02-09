---
title: "Azure Cosmos DB 诊断日志记录 | Microsoft Docs"
description: "借助本教程开始使用 Azure Cosmos DB 日志记录。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: mimig
ms.openlocfilehash: b8f92953634f9294805521d8b925ed67d121a17d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Azure Cosmos DB 诊断日志记录

开始使用一个或多个 Azure Cosmos DB 数据库后，可能需要监视数据库的访问方式和时间。 可利用 Azure Cosmos DB 中的诊断日志记录执行此监视。 启用诊断日志记录，可将日志发送到 [Azure 存储](https://azure.microsoft.com/services/storage/)，将日志流式传输到 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，并/或将日志导出到 [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) 包含的 [Log Analytics](https://azure.microsoft.com/services/log-analytics/)。

![利用 Log Analytics 将诊断日志记录到存储、事件中心或 Operations Management Suite](./media/logging/azure-cosmos-db-logging-overview.png)

在本教程中，开始通过 Azure 门户、CLI 或 PowerShell 使用 Azure Cosmos DB 日志记录。

## <a name="what-is-logged"></a>记录哪些内容？

* 记录所有 API 中所有已经过身份验证的后端请求 (TCP/REST)，包括由于访问权限、系统错误或错误请求而发生的失败请求。 目前不支持用户启动的图形、Cassandra 和表 API 请求。
* 对数据库本身的操作，包括对所有文档、容器和数据库的 CRUD 操作。
* 对帐户密钥的操作，包括创建、修改或删除这些密钥。
* 导致出现 401 响应的未经身份验证的请求。 例如，请求不包含持有者令牌、格式不正确或已过期，或者包含无效的令牌。

## <a name="prerequisites"></a>先决条件
要完成本教程，必须备好以下资源：

* 现有的 Azure Cosmos DB 帐户、数据库和容器。 有关创建这些资源的说明，请参阅[使用 Azure 门户创建数据库帐户](create-sql-api-dotnet.md#create-a-database-account)、[CLI 示例](cli-samples.md)或 [PowerShell 示例](powershell-samples.md)。

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>在 Azure 门户中启用日志记录

1. 在 [Azure 门户](https://portal.azure.com)的 Azure Cosmos DB 帐户中，单击左侧导航栏中的“诊断日志”，然后单击“启用诊断”。

    ![在 Azure 门户中启用 Azure Cosmos DB 的诊断日志记录](./media/logging/turn-on-portal-logging.png)

2. 在“诊断设置”页上，执行以下操作： 

    * **名称**。 为要创建的日志输入名称。

    * **存档到存储帐户**。 要使用此选项，需要一个可连接到的现有存储帐户。 要在门户中创建新的存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md)，并按照说明创建资源管理器（即通用帐户）。 然后在门户中返回到此页，选择存储帐户。 新创建的存储帐户可能几分钟后才会显示在下拉菜单中。
    * **流式传输到事件中心**。 要使用此选项，需要一个可连接到的现有事件中心命名空间和事件中心。 要创建事件中心命名空间，请参阅[使用 Azure 门户创建事件中心命名空间和事件中心](../event-hubs/event-hubs-create.md)。 然后在门户中返回到此页，选择事件中心命名空间和策略名称。
    * **发送到 Log Analytics**。     若要使用此选项，请使用现有的工作区，或遵循[创建新工作区](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace)的步骤在门户中创建新的 Log Analytics 工作区。 有关在 Log Analytics 中查看日志的详细信息，请参阅[在 Log Analytics 中查看日志](#view-in-loganalytics)。
    * **记录 DataPlaneRequests**。 选择此选项可记录从 Azure Cosmos DB 的基础分布式平台发出的针对 SQL、图形、MongoDB、Cassandra 和表 API 帐户的后端请求。 若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后自动删除日期。
    * **记录 MongoRequests**。 选择此选项可记录用户从 Azure Cosmos DB 前端发起的用于处理 MongoDB API 帐户的请求。  若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后自动删除日期。
    * **指标请求**。 选择此选项可在 [Azure 指标](../monitoring-and-diagnostics/monitoring-supported-metrics.md)中存储详细数据。 若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后自动删除日期。

3. 单击“ **保存**”。

    如果收到错误，指出“无法更新诊断 \<工作区名称> 的诊断。 订阅 \<订阅 ID> 未注册为使用 microsoft.insights”， 请遵照[排查 Azure 诊断问题](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)中的说明注册帐户，然后重试此过程。

    若要更改在将来的任意时间点保存诊断日志的方式，可以随时返回到此页，修改帐户的诊断日志设置。

## <a name="turn-on-logging-using-cli"></a>使用 CLI 启用日志记录

若要使用 Azure CLI 启用指标和诊断日志记录，请使用以下命令：

- 若要允许在存储帐户中存储诊断日志，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` 是 Azure Cosmos DB 帐户的名称。 `storageId` 是要将日志发送到的存储帐户的名称。

- 要允许将诊断日志流式传输到事件中心，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` 是 Azure Cosmos DB 帐户的名称。 `serviceBusRuleId` 是采用以下格式的字符串：

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

可以组合这些参数以启用多个输出选项。

## <a name="turn-on-logging-using-powershell"></a>使用 PowerShell 启用日志记录

要使用 PowerShell 启用日志记录，需具备版本最低为 1.0.1 的 Azure Powershell。

要安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

如果已安装 Azure PowerShell，但不知道版本，请在 PowerShell 控制台中键入 `(Get-Module azure -ListAvailable).Version`。  

### <a id="connect"></a>连接到订阅
启动 Azure PowerShell 会话，并使用以下命令登录 Azure 帐户：  

```powershell
Login-AzureRmAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户用户名和密码。 Azure PowerShell 会获取与此帐户关联的所有订阅，并按默认使用第一个订阅。

如果有多个订阅，可能需要指定用来创建 Azure 密钥保管库的特定订阅。 键入以下命令查看帐户的订阅：

```powershell
Get-AzureRmSubscription
```

然后，键入以下命令，以指定与要记录的 Azure Cosmos DB 帐户关联的订阅：

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 如果帐户关联了多个订阅，请务必指定该订阅。
>
>

有关配置 Azure PowerShell 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

### <a id="storage"></a>为日志创建新的存储帐户
尽管可对日志使用现有存储帐户，但在此教程中，我们创建一个专用于 Azure Cosmos DB 日志的新存储帐户。 为方便起见，存储帐户详细信息将存储到名为 sa 的变量中。

为进一步简化管理，我们在本教程中使用包含 Azure Cosmos DB 数据库的同一资源组。 将 ContosoResourceGroup、contosocosmosdblogs 和“美国中北部”的值替换为自己的值（如适用）：

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> 如果决定使用现有存储帐户，则必须使用与 Azure Cosmos DB 订阅相同的订阅，且必须使用资源管理器部署模型而不是经典部署模型。
>
>

### <a id="identify"></a>标识日志的 Azure Cosmos DB 帐户
将 Azure Cosmos DB 帐户名称设置为名为“帐户”的变量，其中 ResourceName 是 Azure Cosmos DB 帐户的名称。

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>启用日志记录
要启用针对 Azure Cosmos DB 的日志记录，请使用 AzureRmDiagnosticSetting cmdlet，同时使用新存储帐户、Azure Cosmos DB 帐户和要为其启用日志的类别的变量。 运行以下命令，将“-Enabled”标志设置为“$true”：

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

该命令的输出应如下所示：

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

此输出确认数据库的日志记录现已启用，系统会将信息保存到存储帐户。

还可以选择性地为日志设置保留期策略，以便自动删除较旧的日志。 例如，使用 **-RetentionEnabled** 标志将保留期策略设置为**$true**，并将 **-RetentionInDays** 参数设置为 **90**，以便自动删除 90 天以上的日志。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>访问日志
DataPlaneRequests 类别的 Azure Cosmos DB 日志存储在所提供的存储帐户的 insights-logs-data-plane-requests 容器中。 

首先，请为容器名称创建一个变量。 该变量将演练的其余部分使用。

```powershell
    $container = 'insights-logs-dataplanerequests'
```

若要列出此容器中的所有 Blob，请键入：

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

输出如下所示：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

可从此输出中看出，blob 遵循以下命名约定：`resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日期和时间值使用 UTC。

由于可使用相同的存储帐户收集多个资源的日志，因此 blob 名称中完全限定的资源 ID 很适合用于访问或下载所需的 blob。 但在这样做之前，让我们先了解如何下载所有 Blob。

首先，创建一个文件夹用于下载 Blob。 例如：

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

然后获取所有 blob 的列表：  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

通过 'Get-AzureStorageBlobContent' 以管道传送此列表，将 Blob 下载到目标文件夹：

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

运行第二个命令时，blob 名称中的 / 分隔符会在目标文件夹下创建完整的文件夹结构。 此文件夹结构用于下载 blob 并将其存储为文件。

若要选择性地下载 Blob，请使用通配符。 例如：

* 如果有多个数据库，且只想下载 CONTOSOCOSMOSDB3 数据库的日志：

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* 如果有多个资源组，并只想要下载其中某个资源组的日志，请使用 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* 如果要下载 2017 年 7 月的所有日志，请使用 `-Blob '*/year=2017/m=07/*'`：

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

此外：

* 若要查询数据库资源的诊断设置状态：`Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* 若要禁用数据库帐户资源的 DataPlaneRequests 类别的日志记录：`Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


在其中每个查询中返回的 Blob 存储为文本，采用 JSON Blob 格式，如以下代码中所示。 

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

若要了解每个 JSON Blob 中的数据，请参阅[解释 Azure Cosmos DB 日志](#interpret)。

## <a name="managing-your-logs"></a>管理日志

自执行 Azure Cosmos DB 操作起两小时，即可在帐户中使用日志。 存储帐户中的日志完全由你管理：

* 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
* 删除不想继续保留在存储帐户中的日志。
* 选择 Log DataPlaneRequests 后，在门户中配置存档到存储帐户的数据平面请求的保留期。 要更改此设置，请参阅[在 Azure 门户中启用日志记录](#turn-on-logging-in-the-azure-portal)。


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>在 Log Analytics 中查看日志

如果在启用日志记录时选择了“发送到 Log Analytics”选项，则集合中的诊断数据会在两个小时内转发到 Log Analytics。 这意味着，如果在启用日志记录之后立即查看 Log Analytics，将看不到任何数据。 请等待两个小时并重试。 

在查看日志之前，需要检查并确定 Log Analytics 工作区是否已升级为使用新的 Log Analytics 查询语言。 若要检查，请打开 [Azure 门户](https://portal.azure.com)，在最左侧单击“Log Analytics”，选择工作区名称，如下图所示。 此时会显示“OMS 工作区”页，如下图所示。

![Azure 门户中的 Log Analytics](./media/logging/azure-portal.png)

如果“OMS 工作区”页上显示以下消息，则表示工作区尚未升级为使用新语言。 有关升级到新查询语言的详细信息，请参阅[将 Azure Log Analytics 工作区升级到新的日志搜索](../log-analytics/log-analytics-log-search-upgrade.md)。 

![Log Analytics 升级通知](./media/logging/upgrade-notification.png)

若要在 Log Analytics 中查看诊断数据，请从左侧菜单或页面的“管理”区域打开“日志搜索”页，如下图所示。

![Azure 门户中的“日志搜索”选项](./media/logging/log-analytics-open-log-search.png)

启用数据收集后，使用新的查询语言运行以下日志搜索示例，以查看最近的 10 条日志 `AzureDiagnostics | take 10`。

![示例中包含 10 个日志搜索](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>查询

可在“日志搜索”框中输入下面这些附加的查询，以帮助监视 Azure Cosmos DB 容器。 这些查询使用[新语言](../log-analytics/log-analytics-log-search-upgrade.md)。 

若要了解每个日志搜索返回的数据的含义，请参阅[解释 Azure Cosmos DB 日志](#interpret)。

* 在指定的时间段内来自 Azure Cosmos DB 的所有诊断日志。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* 10 个最近记录的事件。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* 已按操作类型分组的所有操作。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* 已按资源分组的所有操作。

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* 已按资源分组的所有用户活动。 请注意，这是活动日志，而不是诊断日志。

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* 哪些操作花费的时间超过 3 毫秒。

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 哪些代理正在运行操作。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* 何时执行了长时间运行的操作。

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

有关使用新日志搜索语言的其他信息，请参阅[了解 Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-search-new.md)。 

## <a id="interpret"></a>解释日志

存储在 Azure 存储和 Log Analytics 中的诊断数据使用非常相似的架构。 

下表描述了每个日志条目的内容。

| Azure 存储字段或属性 | Log Analytics 属性 | 说明 |
| --- | --- | --- |
| time | TimeGenerated | 操作发生时的日期和时间 (UTC)。 |
| resourceId | 资源 | 为其启用日志的 Azure Cosmos DB 帐户。|
| category | 类别 | 对于 Azure Cosmos DB 日志，DataPlaneRequests 是唯一可用的值。 |
| operationName | OperationName | 操作的名称。 此值可以是以下任意操作：创建、更新、读取、ReadFeed、删除、替换、执行、SqlQuery、查询、JSQuery、Head、HeadFeed 或 Upsert。   |
| 属性 | 不适用 | 下列行中描述了此字段的内容。 |
| activityId | activityId_g | 日志记录操作的唯一 GUID。 |
| userAgent | userAgent_s | 一个字符串，指定执行请求的客户端用户代理。 格式为 {用户代理名}/{版本}。|
| resourceType | ResourceType | 所访问资源的类型。 此值可以是以下任意资源类型：数据库、集合、文档、附件、用户、权限、StoredProcedure、触发器、UserDefinedFunction 或产品/服务。 |
| statusCode |statusCode_s | 操作的响应状态。 |
| requestResourceId | ResourceId | 与请求相关的 resourceId，可能指向 databaseRid、collectionRid 或 documentRid（具体取决于所执行的操作）。|
| clientIpAddress | clientIpAddress_s | 客户端的 IP 地址。 |
| requestCharge | requestCharge_s | 操作所用 RU 数 |
| collectionRid | collectionId_s | 集合的唯一 ID。|
| duration | duration_s | 操作的持续时间（按滴答数计）。 |
| requestLength | requestLength_s | 请求的长度（按字节计）。 |
| responseLength | responseLength_s | 响应的长度（按字节计）。|
| resourceTokenUserRid | resourceTokenUserRid_s | [资源令牌](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)用于身份验证并指向用户的资源 ID 时，此项非空。 |

## <a name="next-steps"></a>后续步骤

- 若要了解如何启用日志记录，以及各种 Azure 服务支持的指标和日志类别，请参阅 [Microsoft Azure 中的指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和 [Azure 诊断日志概述](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)一文。
- 阅读以下文章，了解事件中心：
   - [什么是 Azure 事件中心？](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- 参阅[从 Azure 存储下载指标和诊断日志](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
- 参阅[了解 Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-search-new.md)
