---
title: Azure Cosmos DB 中的诊断日志记录
description: 了解对 Azure Cosmos DB 中存储的数据进行记录和监视的各种方法。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 41fa5a859e738c2bb70e4885aa856f247e922492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448996"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Azure Cosmos DB 中的诊断日志记录 

开始使用一个或多个 Azure Cosmos DB 数据库后，可能需要监视数据库的访问方式和时间。 本文概述了 Azure 平台上提供的日志。 其中介绍了如何启用监视用的诊断日志记录，以便将日志发送到 [Azure 存储](https://azure.microsoft.com/services/storage/)，将日志流式传输到 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)，以及如何将日志导出到 [Azure Monitor 日志](https://azure.microsoft.com/services/log-analytics/)。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>在 Azure 中可用的日志

在探讨如何监视 Azure Cosmos DB 帐户之前，让我们先澄清一些有关日志记录和监视的事项。 Azure 平台上有不同类型的日志。 有 [Azure 活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)、[Azure 诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)、[Azure 指标](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)日志、事件日志、检测信号监视日志、操作日志，等等。 有很多种日志。 可以在 Azure 门户的 [Azure Monitor 日志](https://azure.microsoft.com/services/log-analytics/)中看到日志的完整列表。 

下图显示所提供的不同种类的 Azure 日志：

![不同种类的 Azure 日志](./media/logging/azurelogging.png)

在上图中，**计算资源**表示可以访问其 Microsoft 来宾 OS 的 Azure 资源。 例如，Azure 虚拟机、虚拟机规模集、Azure 容器服务等都视为计算资源。 计算资源生成活动日志、诊断日志和应用程序日志。 若要了解详细信息，请参阅 [Azure 中的监视数据源](../azure-monitor/platform/data-sources.md)一文。

“非计算资源”是在其中无法访问基础 OS，但可直接使用资源的资源  。 例如网络安全组、逻辑应用等。 Azure Cosmos DB 是一种非计算资源。 可以在活动日志中或通过在门户中启用“诊断日志”选项查看非计算资源的日志。 若要了解详细信息，请参阅 [Azure Monitor 中的数据源](../azure-monitor/platform/data-sources.md)一文。

活动日志记录 Azure Cosmos DB 订阅级别的操作。 将记录 ListKeys、Write DatabaseAccounts 等操作。 诊断日志提供更精细的日志记录并允许记录 DataPlaneRequests（创建、读取、查询等）和 MongoRequests。


本文重点讨论 Azure 活动日志、Azure 诊断日志和 Azure 指标。 这三类日志有何差异？ 

### <a name="azure-activity-log"></a>Azure 活动日志

Azure 活动日志是一种方便用户深入了解 Azure 中发生的订阅级别事件的订阅日志。 活动日志在“管理”类别下报告订阅的控制平面事件。 通过活动日志，可确定订阅中资源上进行的任何写入操作 (PUT, POST, DELETE) 的“什么操作、谁操作和操作时间”等信息。 还可以了解该操作和其他相关属性的状态。 

活动日志不同于诊断日志。 活动日志提供有关从外部（“控制面”）对资源所执行操作的数据。  在 Azure Cosmos DB 上下文中，控制平面操作包括创建容器、列出密钥、删除密钥、列出数据库，等等。 诊断日志由资源发出，并提供有关该资源（“数据平面”）的操作信息。  诊断日志中部分数据平面操作的示例包括 Delete、Insert 和 ReadFeed。

活动日志（控制平面操作）在本质上可能要丰富得多，可能包括：调用方的完整电子邮件地址、调用方 IP 地址、资源名称、操作名称、TenantId，等等。 活动日志包含多个数据[类别](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema)。 有关这些类别的架构的完整详细信息，请参阅 [Azure 活动日志事件架构](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema)。 但是，诊断日志在本质上可能是限制性的，因为通常会将个人数据从这些日志中剥离出来。 因此，你可能有调用方的 IP 地址，但最后的八进制数已删除。

### <a name="azure-metrics"></a>Azure 指标

[Azure 指标](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)，包含最重要的 Azure 遥测数据类型（也称为“性能计数器”），是大多数 Azure 资源发出的。  可以通过指标来查看与吞吐量、存储、一致性、可用性以及 Azure Cosmos DB 资源的延迟相关的信息。 有关详细信息，请参阅[使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)。

### <a name="azure-diagnostic-logs"></a>Azure 诊断日志

Azure 诊断日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 这些日志会捕获每个请求。 这些日志的内容因资源类型而异。 资源级诊断日志与来宾 OS 级诊断日志也不相同。 来宾 OS 级诊断日志由在虚拟机内部或其他受支持的资源类型中运行的代理收集。 资源级诊断日志不需要代理并从 Azure 平台本身捕获特定于资源的数据。 来宾 OS 级诊断日志从操作系统和在虚拟机上运行的应用程序捕获数据。

![存储、事件中心或 Azure Monitor 日志的诊断日志记录](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Azure 诊断日志记录哪些内容？

* 记录所有 API 中所有已经过身份验证的后端请求 (TCP/REST)，包括由于访问权限、系统错误或错误请求而发生的失败请求。 目前不支持用户启动的图形、Cassandra 和表 API 请求。
* 对数据库本身的操作，包括对所有文档、容器和数据库的 CRUD 操作。
* 对帐户密钥的操作，包括创建、修改或删除这些密钥。
* 导致出现 401 响应的未经身份验证的请求。 例如，请求不包含持有者令牌、格式不正确或已过期，或者包含无效的令牌。

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>在 Azure 门户中启用日志记录

使用以下步骤启用 Azure 门户中诊断日志记录：

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 导航到 Azure Cosmos 帐户。 打开**诊断设置**窗格中，并选择**添加诊断设置**选项。

    ![在 Azure 门户中启用 Azure Cosmos DB 的诊断日志记录](./media/logging/turn-on-portal-logging.png)

1. 在中**诊断设置**页上，在窗体中填写以下详细信息： 

    * **名称**：为要创建的日志输入名称。

    * 您可以存储到以下服务的日志：

      * **存档到存储帐户**：要使用此选项，需要一个可连接到的现有存储帐户。 若要在门户中创建新的存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md)一文。 然后，返回到门户，选择存储帐户中的 Azure Cosmos Db 诊断设置窗格。 新创建的存储帐户可能几分钟后才会显示在下拉菜单中。

      * **流式传输到事件中心**：要使用此选项，需要一个可连接到的现有事件中心命名空间和事件中心。 要创建事件中心命名空间，请参阅[使用 Azure 门户创建事件中心命名空间和事件中心](../event-hubs/event-hubs-create.md)。 然后，返回到此页在门户中选择事件中心命名空间和策略名称。

      * **发送到 Log Analytics**：若要使用此选项，请使用现有的工作区，或遵循[创建新工作区](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)的步骤在门户中创建新的 Log Analytics 工作区。 

   * 可以记录以下数据：

      * **DataPlaneRequests**:选择此选项可记录到所有 Api 的后端请求的 Azure Cosmos DB 中包括 SQL、 图形、 MongoDB、 Cassandra 和表 API 帐户。 若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后自动删除日志。 以下 JSON 数据是使用 DataPlaneRequests 记入日志的详细信息的示例输出。 需要注意的关键属性包括：Requestcharge、 statusCode、 clientIPaddress，和 partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**:选择此选项可从前端来为请求提供服务对 Azure Cosmos DB API for MongoDB 中记录用户发起的请求。 MongoDB 请求将出现在 MongoRequests 以及 DataPlaneRequests。 若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后自动删除日志。 以下 JSON 数据是使用 MongoRequests 记入日志的详细信息的示例输出。 需要注意的关键属性包括：Requestcharge，操作码：

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**:选择此选项可记录已执行的查询文本。  以下 JSON 数据是使用 QueryRuntimeStatistics 记入日志的详细信息的示例输出：

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **指标请求**：选择此选项可在 [Azure 指标](../azure-monitor/platform/metrics-supported.md)中存储详细数据。 若要存档到存储帐户，可以选择诊断日志的保留期。 保留期到期后自动删除日志。

3. 选择“保存”。 

    如果收到错误，指出“无法更新诊断 \<工作区名称> 的诊断。 订阅 \<订阅 ID> 未注册为使用 microsoft.insights”，请遵照[排查 Azure 诊断问题](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)中的说明注册帐户，然后重试此过程。

    若要更改在将来的任意时间点保存诊断日志的方式，可以返回到此页，修改帐户的诊断日志设置。

## <a name="turn-on-logging-by-using-azure-cli"></a>使用 Azure CLI 启用日志记录

若要使用 Azure CLI 启用指标和诊断日志记录，请使用以下命令：

- 若要允许在存储帐户中存储诊断日志，请使用以下命令：

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` 是 Azure Cosmos DB 帐户的名称。 资源采用的格式为“/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/<Azure_Cosmos_account_name>” `storage-account` 是要将日志发送到的存储帐户的名称。 将类别参数值更新为 "MongoRequests" 或 "DataPlaneRequests" 即可记录其他日志。 

- 要允许将诊断日志流式传输到事件中心，请使用以下命令：

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` 是 Azure Cosmos DB 帐户的名称。 `event-hub-rule` 是事件中心规则 ID。 

- 若要启用将诊断日志发送到 Log Analytics 工作区，请使用以下命令：

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

可以组合这些参数以启用多个输出选项。

## <a name="turn-on-logging-by-using-powershell"></a>使用 PowerShell 启用日志记录

若要使用 PowerShell 启用诊断日志记录，需要版本最低为 1.0.1 的 Azure Powershell。

要安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

如果已安装 Azure PowerShell，但不知道版本，请在 PowerShell 控制台中键入 `(Get-Module azure -ListAvailable).Version`。  

### <a id="connect"></a>连接到订阅
启动 Azure PowerShell 会话，并使用以下命令登录 Azure 帐户：  

```powershell
Connect-AzAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户用户名和密码。 Azure PowerShell 会获取与此帐户关联的所有订阅，并按默认使用第一个订阅。

如果有多个订阅，可能需要指定用来创建 Azure Key Vault 的特定订阅。 若要查看帐户的订阅，请键入以下命令：

```powershell
Get-AzSubscription
```

若要指定与要记录的 Azure Cosmos DB 帐户关联的订阅，请键入以下命令：

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 如果帐户关联了多个订阅，请务必指定要使用的订阅。
>
>

有关如何配置 Azure PowerShell 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

### <a id="storage"></a>为日志创建新的存储帐户
尽管可对日志使用现有存储帐户，但在此教程中，我们创建一个专用于 Azure Cosmos DB 日志的新存储帐户。 为方便起见，存储帐户详细信息将存储到名为 **sa** 的变量中。

为进一步简化管理，我们在本教程中使用包含 Azure Cosmos DB 数据库的同一资源组。 将 **ContosoResourceGroup**、**contosocosmosdblogs** 和 **North Central US** 参数替换为自己的值（如适用）：

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> 如果决定使用现有的存储帐户，该帐户必须使用与 Azure Cosmos DB 订阅相同的订阅。 该帐户还必须使用资源管理器部署模型，而不是经典部署模型。
>
>

### <a id="identify"></a>标识日志的 Azure Cosmos DB 帐户
将 Azure Cosmos DB 帐户名称设置为名为 **account** 的变量，其中 **ResourceName** 是 Azure Cosmos DB 帐户的名称。

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>启用日志记录
若要启用针对 Azure Cosmos DB 的日志记录，请使用 `Set-AzDiagnosticSetting` cmdlet，同时使用新存储帐户、Azure Cosmos DB 帐户和要为其启用日志的类别的变量。 运行以下命令，并将 **-Enabled** 标志设置为 **$true**：

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

该命令的输出应如以下示例所示：

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

该命令的输出确认数据库的日志记录现已启用，系统正在将信息保存到存储帐户。

还可以选择性地为日志设置保留期策略，以便自动删除较旧的日志。 例如，将 **-RetentionEnabled** 标志设置为 **$true**，以设置保留策略。 将 **-RetentionInDays** 参数设置为 **90**，以便自动删除超过 90 天的日志。

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>访问日志
Azure Cosmos DB 日志**DataPlaneRequests**类别存储在**insights 日志 dataplanerequests**你提供的存储帐户中的容器。 

首先，请为容器名称创建一个变量。 该变量将在整个演练中使用。

```powershell
    $container = 'insights-logs-dataplanerequests'
```

若要列出此容器中的所有 Blob，请键入：

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

该命令的输出应如以下示例所示：

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

由于可使用相同的存储帐户收集多个资源的日志，因此，可以使用 Blob 名称中完全限定的资源 ID 来访问或下载所需的特定 Blob。 但在这样做之前，让我们先了解如何下载所有 Blob。

首先，创建一个文件夹用于下载 Blob。 例如：

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

然后获取所有 Blob 的列表：  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

通过 `Get-AzStorageBlobContent` 命令以管道传送此列表，将 Blob 下载到目标文件夹：

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

运行第二个命令时，blob 名称中的 / 分隔符会在目标文件夹下创建完整的文件夹结构  。 此文件夹结构用于下载 blob 并将其存储为文件。

若要选择性地下载 Blob，请使用通配符。 例如：

* 如果有多个数据库，且只想下载名为 **CONTOSOCOSMOSDB3** 的数据库的日志，请使用以下命令：

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* 如果有多个资源组，并只想要下载其中某个资源组的日志，请使用命令 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* 如果要下载 2017 年 7 月的所有日志，请使用命令 `-Blob '*/year=2017/m=07/*'`：

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

也可以运行以下命令：

* 若要查询数据库资源的诊断设置状态，请使用命令 `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`。
* 若要禁用数据库帐户资源的 **DataPlaneRequests** 类别的日志记录，请使用命令 `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`。


在其中每个查询中返回的 Blob 存储为文本，采用 JSON Blob 格式，如以下代码中所示：

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

## <a name="manage-your-logs"></a>管理日志

自执行 Azure Cosmos DB 操作起两小时，即可在帐户中使用诊断日志。 存储帐户中的日志完全由你管理：

* 请使用标准的 Azure 访问控制方法保护日志并限制可访问日志的人员。
* 删除不想继续保留在存储帐户中的日志。
* 选择“记录 DataPlaneRequests”设置后，在门户中配置存档到存储帐户的数据平面请求的保留期。  要更改此设置，请参阅[在 Azure 门户中启用日志记录](#turn-on-logging-in-the-azure-portal)。


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>查看 Azure Monitor 日志中的日志

如果在启用诊断日志记录时选择了“发送到 Log Analytics”选项，则容器中的诊断数据会在两个小时内转发到 Azure Monitor 日志  。 如果在启用日志记录之后立即查看 Azure Monitor 日志，将看不到任何数据。 请等待两个小时并重试。 

在查看日志之前，请检查并确定 Log Analytics 工作区是否已升级为使用新的 Kusto 查询语言。 若要检查，请打开 [Azure 门户](https://portal.azure.com)，在最左侧选择“Log Analytics 工作区”，然后选择工作区名称，如下图所示。  此时会显示“Log Analytics 工作区”  页：

![Azure 门户中的 Azure Monitor 日志](./media/logging/azure-portal.png)

>[!NOTE]
>OMS 工作区现在称为 Log Analytics 工作区。  

如果“Log Analytics 工作区”  页上显示以下消息，则表示工作区尚未升级为使用新语言。 有关如何升级到新查询语言的详细信息，请参阅[将 Azure Log Analytics 工作区升级到新的日志搜索](../log-analytics/log-analytics-log-search-upgrade.md)。 

![Azure Monitor 日志升级消息](./media/logging/upgrade-notification.png)

若要查看 Azure Monitor 日志中的诊断数据，请打开左侧菜单中的“日志搜索”页或该页的“管理”区域，如下图所示：  

![Azure 门户中的“日志搜索”选项](./media/logging/log-analytics-open-log-search.png)

启用数据收集后，使用新的查询语言运行以下日志搜索示例，以查看最近的 10 条日志 `AzureDiagnostics | take 10`。

![搜索最近 10 条日志的示例](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>查询

可在“日志搜索”框中输入下面这些附加的查询，以帮助监视 Azure Cosmos DB 容器。  这些查询使用[新语言](../log-analytics/log-analytics-log-search-upgrade.md)。 

若要了解每个日志搜索返回的数据的含义，请参阅[解释 Azure Cosmos DB 日志](#interpret)。

* 若要查询指定时间段内来自 Azure Cosmos DB 的所有诊断日志，请执行以下操作：

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* 查询最近记录的 10 个事件：

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* 查询已按操作类型分组的所有操作：

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* 查询已按**资源**分组的所有操作：

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* 查询已按资源分组的所有用户活动：

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > 此命令适用于活动日志，而不适用于诊断日志。

* 查询哪些操作花费的时间超过 3 毫秒：

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 查询哪些代理正在运行操作：

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* 查询何时执行了长时间运行的操作：

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

有关如何使用新的日志搜索语言的详细信息，请参阅[了解 Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-search-new.md)。 

## <a id="interpret"></a>解释日志

存储在 Azure 存储和 Azure Monitor 日志中的诊断数据使用相似的架构。 

下表描述了每个日志条目的内容。

| Azure 存储字段或属性 | Azure Monitor 日志属性 | 描述 |
| --- | --- | --- |
| **time** | **TimeGenerated** | 操作发生时的日期和时间 (UTC)。 |
| **resourceId** | **资源** | 为其启用日志的 Azure Cosmos DB 帐户。|
| **category** | **类别** | 对于 Azure Cosmos DB 日志，**DataPlaneRequests** 是唯一可用的值。 |
| **operationName** | **OperationName** | 操作的名称。 此值可以是以下任意操作：创建、更新、读取、ReadFeed、删除、替换、执行、SqlQuery、查询、JSQuery、Head、HeadFeed 或 Upsert。   |
| **properties** | 不适用 | 下面的行中描述了此字段的内容。 |
| **activityId** | **activityId_g** | 日志记录操作的唯一 GUID。 |
| **userAgent** | **userAgent_s** | 一个字符串，指定执行请求的客户端用户代理。 格式为 {用户代理名}/{版本}。|
| **requestResourceType** | **requestResourceType_s** | 所访问资源的类型。 此值可以是以下任意资源类型：数据库、容器、文档、附件、用户、权限、StoredProcedure、触发器、UserDefinedFunction 或产品/服务。 |
| **statusCode** | **statusCode_s** | 操作的响应状态。 |
| **requestResourceId** | **ResourceId** | 与请求相关的 resourceId。 值可以指向 databaseRid、collectionRid 或 documentRid（具体取决于所执行的操作）。|
| **clientIpAddress** | **clientIpAddress_s** | 客户端的 IP 地址。 |
| **requestCharge** | **requestCharge_s** | 操作使用的 RU 数目 |
| **collectionRid** | **collectionId_s** | 集合的唯一 ID。|
| **duration** | **duration_s** | 操作持续时间（按时钟周期计）。 |
| **requestLength** | **requestLength_s** | 请求的长度（按字节计）。 |
| **responseLength** | **responseLength_s** | 响应的长度（按字节计）。|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 将[资源令牌](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)用于身份验证时，此值非空。 值指向用户的资源 ID。 |

## <a name="next-steps"></a>后续步骤

- 若要了解如何启用日志记录，以及各种 Azure 服务支持的指标和日志类别，请参阅文章 [Microsoft Azure 中的指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和 [Azure 诊断日志概述](../azure-monitor/platform/diagnostic-logs-overview.md)。
- 阅读以下文章，了解事件中心：
   - [什么是 Azure 事件中心？](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- 参阅[从 Azure 存储下载指标和诊断日志](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs)。
- 读取[了解 Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-search-new.md)。
