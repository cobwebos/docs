---
title: Azure 存储分析指标 （经典）
description: 了解如何在 Azure 存储中使用度量值。
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: b35d3d22ce154420c9099143894688389e8af420
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2019
ms.locfileid: "58078034"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 存储分析指标 （经典）

存储分析可存储一些度量值，这些度量值包括有关存储服务请求的聚合事务统计信息和容量数据。 在 API 操作级别以及存储服务级别报告事务，并在存储服务级别报告容量。 度量值数据可用于分析存储服务使用情况，诊断对存储服务所发出请求的问题以及提高使用服务的应用程序的性能。  

 默认为新存储帐户启用存储分析指标。 你可以配置中的指标[Azure 门户](https://portal.azure.com/); 有关详细信息，请参阅[监视 Azure 门户中的存储帐户](/azure/storage/storage-monitor-storage-account)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用设置服务属性操作为每个服务启用存储分析。  

> [!NOTE]
> 存储分析度量值是可用于 Blob、 队列、 表和文件服务。
> 存储分析度量值现在是经典指标。 Microsoft 建议使用[Azure Monitor 中的存储指标](/azure/storage/common/storage-metrics-in-azure-monitor.md)而不是存储分析度量值。

## <a name="transaction-metrics"></a>事务度量值  
 对于每个存储服务和请求的 API 操作，以小时或分钟为间隔记录一组可靠的数据，其中包括入口/出口、可用性、错误和分类请求百分比。 可以在[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema.md)主题中查看事务详细信息的完整列表。  

 在两个级别记录事务数据 – 服务级别和 API 操作级别。 在服务级别，汇总所有请求的 API 操作的统计信息将每小时写入一次表实体，即使未向服务发出请求也是如此。 在 API 操作级别，仅当在该小时内请求操作时才将统计信息写入实体。  

 例如，如果对 Blob 服务执行 **GetBlob** 操作，则存储分析指标将记录请求并将其包含在 Blob 服务以及 **GetBlob** 操作的聚合数据中。 但是，如果没有**GetBlob**在一小时内请求操作，实体将不会写入 *$MetricsTransactionsBlob*为该操作。  

 为用户请求和存储分析本身发出的请求记录事务度量值。 例如，将记录存储分析写入日志和表实体的请求。

## <a name="capacity-metrics"></a>容量度量值  

> [!NOTE]
>  目前，容量度量值仅适用于 BLOB 服务。

 每天记录存储帐户的 BLOB 服务的容量数据，并写入两个表实体。 一个实体提供用户数据的统计信息，另一个实体提供有关存储分析所使用的 `$logs` Blob 容器的统计信息。 *$MetricsCapacityBlob*表包含以下统计信息：  

- **容量**：使用存储帐户的 Blob 服务，以字节为单位的存储量。  
- **ContainerCount**：存储帐户的 Blob 服务中的 blob 容器数。  
- **ObjectCount**：在存储帐户的 Blob 服务中的提交和未提交块或页 blob 的数量。  

  有关容量指标的详细信息，请参阅[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema.md)。  

## <a name="how-metrics-are-stored"></a>如何存储度量值  

 每个存储服务的所有度量数据都存储在为该服务保留的三个表中：一个表存储事务信息，一个表存储分钟事务信息，还有一个表存储容量信息。 事务和分钟事务信息由请求和响应数据组成，而容量信息由存储使用情况数据组成。 小时指标、 分钟度量值和帐户的 Blob 服务可以访问以下表中所述命名的表中的存储容量。  

|指标级别|表名|支持的版本|  
|-------------------|-----------------|----------------------------|  
|每小时度量值，主位置|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|仅限 2013-08-15 之前的版本。 虽然仍然支持这些名称，但还是建议改为使用下面列出的表。|  
|每小时度量值，主位置|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|所有版本。 文件服务度量值的支持是仅在版本 2015年-04-05 中可用及更高版本。|  
|分钟度量值，主位置|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|所有版本。 文件服务度量值的支持是仅在版本 2015年-04-05 中可用及更高版本。|  
|每小时度量值，辅助位置|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|所有版本。 必须启用读访问的异地冗余复制。|  
|分钟度量值，辅助位置|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|所有版本。 必须启用读访问的异地冗余复制。|  
|容量（仅限 Blob 服务）|$MetricsCapacityBlob|所有版本。|  

 为存储服务终结点启用存储分析时，会自动创建这些表。 它们均可以通过访问的命名空间的存储帐户，例如： `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`。 度量值表不会出现在列表操作中，并必须直接通过表名进行访问。  

## <a name="enable-metrics-using-the-azure-portal"></a>使用 Azure 门户启用指标
请按照下列步骤在 [Azure 门户](https://portal.azure.com)中启用指标：

1. 导航到存储帐户。
1. 选择**诊断设置 （经典）** 从**菜单**窗格。
1. 确保“状态”设置为“打开”。
1. 选择希望监视的服务的度量值。
1. 指定用来指示保留度量值和日志数据的时间长度的保留期策略。
1. 选择“保存”。

[Azure 门户](https://portal.azure.com)目前不允许在存储帐户中配置分钟指标；必须通过 PowerShell 或编程方式启用分钟指标。

> [!NOTE]
>  请注意，在 Azure 门户目前不允许您在您的存储帐户中配置分钟度量值。 必须启用分钟度量值使用 PowerShell 或以编程方式。  

## <a name="enable-storage-metrics-using-powershell"></a>使用 PowerShell 启用存储指标  
可以在本地计算机上使用 PowerShell 通过使用 Azure PowerShell cmdlet 在存储帐户中配置存储指标**Get-azurestorageservicemetricsproperty**检索当前设置，然后 cmdlet **Set-azurestorageservicemetricsproperty**若要更改当前设置。  

控制存储度量值的 cmdlet 使用以下参数：  

* **ServiceType**，可能值是**Blob**，**队列**，**表**，并且**文件**。
* **MetricsType**，可能的值为**小时**并**分钟**。  
* **MetricsLevel**，可能的值为：
* **无**：关闭监视。
* **服务**：收集入口/出口、 可用性、 延迟和经过汇总的有关 blob、 队列、 表和文件服务成功百分比等度量值。
* **ServiceAndApi**:除了服务指标收集一组相同的 Azure 存储服务 API 中每项存储操作的指标。

例如，以下命令在保留期设为 5 天的情况下，在默认存储帐户中为 Blob 服务打开分钟度量值：  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

以下命令在默认存储帐户中为 Blob 服务检索当前的小时度量值级别和保留天数：  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

若要了解如何配置 Azure PowerShell cmdlet 来使用 Azure 订阅并了解如何选择要使用的默认存储帐户，请参阅：[如何安装和配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

## <a name="enable-storage-metrics-programmatically"></a>以编程方式启用存储度量值  
除了使用 Azure 门户或 Azure PowerShell cmdlet 控制存储度量值，您还可以使用 Azure 存储 Api 之一。 例如，如果使用的一种.NET 语言可以使用存储客户端库。  

类**CloudBlobClient**， **CloudQueueClient**， **CloudTableClient**，以及**CloudFileClient**所有具有之类的方法**使用 SetServiceProperties**并**SetServicePropertiesAsync**采用**ServiceProperties**对象作为参数。 可以使用**ServiceProperties**对象配置存储度量值。 例如，以下C#代码片段演示如何更改小时队列指标的指标级别和保留天数：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

有关使用.NET 语言配置存储度量值的详细信息，请参阅[适用于.NET 的存储客户端库](https://msdn.microsoft.com/library/azure/mt347887.aspx)。  

有关配置存储度量值使用 REST API 的常规信息，请参阅[启用和配置存储分析](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics.md)。  

##  <a name="viewing-storage-metrics"></a>查看存储度量值  
在将存储分析度量值配置为监视存储帐户后，存储分析将使用存储帐户在一组已知表中记录度量值。 可以将图表配置为每小时查看 [Azure 门户](https://portal.azure.com)中的指标：

1. 导航到 [Azure 门户](https://portal.azure.com)中的存储帐户。
1. 选择**指标 （经典）** 中**菜单**边栏选项卡中你想要查看其指标的服务。
1. 单击你想要配置的图表。
1. 在“编辑图表”边栏选项卡中，选择“时间范围”、“图表类型”，以及想要在图表中显示的指标。

在中**监视 （经典）** 部分中的存储帐户的菜单边栏选项卡在 Azure 门户中，你可以配置[警报规则](#metrics-alerts)，例如发送电子邮件警报以通知你时特定的度量值达到某些值。

如果你想要下载指标供长期存储或本地对其进行分析，则必须使用工具或编写一些代码来读取表。 必须下载分析用的分钟度量值。 如果在存储帐户中列出所有表，则这些表不会显示，但可以按名称直接访问它们。 很多存储浏览工具都识别这些表，可用于直接查看它们 (请参阅[Azure 存储客户端工具](/azure/storage/storage-explorers)有关可用工具的列表)。

||||  
|-|-|-|  
|**指标**|**表名**|**说明**|  
|每小时度量值|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|在 2013年-08-15 之前的版本，这些表称为：<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> 文件服务的指标是版本 2015年-04-05 开始可用。|  
|分钟度量值|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|仅可使用 PowerShell 启用或以编程方式。<br /><br /> 文件服务的指标是版本 2015年-04-05 开始可用。|  
|容量|$MetricsCapacityBlob|仅限 blob 服务。|  

有关这些表的完整架构详细信息，请参阅 [Storage Analytics Metrics Table Schema](/rest/api/storageservices/storage-analytics-metrics-table-schema.md)（存储分析度量值表架构）。 以下示例行仅显示一部分可用列，但也说明了存储度量值在采用相应方式保存这些度量值时展现的一些重要功能：  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**可用性**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|第|第|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|第|第|771|217|100|9|6|100|  

在这个分钟度量值数据示例中，分区键按分钟使用时间。 行键可识别行中存储的信息的类型，其中包含两条信息，即访问类型和请求类型：  

-   访问类型是**用户**或**系统**，其中**用户**指的是向存储服务的所有用户请求并**系统**都引用存储分析发出的请求。  

-   请求类型是**所有**在这种情况下是摘要行或其标识的特定 API，如**QueryEntity**或**UpdateEntity**。  

上面的示例数据显示的一分钟 （从上午 11:00），因此所有记录的数目**QueryEntities**请求数加**QueryEntity**请求数加**UpdateEntity**请求添加最多为 7，这是总计上所示**user: All**行。 同样，可以在派生的平均端到端延迟为 104.4286**用户： All**通过计算行 ((143.8 * 5) + 3 + 9) / 7。  

## <a name="metrics-alerts"></a>度量警报
应考虑中的设置警报[Azure 门户](https://portal.azure.com)因此，将自动通知你存储服务行为的重要更改。 如果使用存储资源管理器工具下载这种采用分隔格式的指标数据，则可以使用 Microsoft Excel 分析数据。 有关可用存储资源管理器工具的列表，请参阅 [Azure 存储客户端工具](/azure/storage/storage-explorers)。 可以配置中的警报**警报 （经典）** 边栏选项卡，访问**监视 （经典）** 在存储帐户菜单边栏选项卡。

> [!IMPORTANT]
> 在存储事件与记录对应每小时或分钟度量数据的时间之间可能存在延迟。 对于分钟度量，可能会一次写入几分钟的数据。 这可能会导致将前面几分钟的事务聚合到当前分钟的事务中。 发生此情况时，警报服务可能没有已配置警报间隔内的所有可用度量数据，这可能会导致意外触发警报。
>

## <a name="accessing-metrics-data-programmatically"></a>以编程方式访问度量值数据  
以下列表显示示例 C# 代码，该代码用于访问分钟范围的分钟度量值，并在控制台窗口中显示结果。 该代码示例使用 Azure 存储客户端库版本 4.x 或更高版本，其中包括**CloudAnalyticsClient**类，用于简化访问存储中的度量值表。  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>在存储度量值的计费  
为度量值创建表实体的写入请求，按适用于所有 Azure 存储操作的标准费率收费。  

读取和删除请求的客户端的度量值数据也是标准费率计费。 如果已配置数据保留策略，则当 Azure 存储删除旧的度量值数据时，不用付费。 但是，如果删除分析数据，则会针对删除操作向帐户收费。  

度量值表使用的容量也是计费的。 可以使用以下内容估算用于存储度量值数据的容量：  

-   如果每小时服务利用每个服务中的每个 API，大约 148KB 的数据存储在度量值事务表中每隔一小时，如果已启用这两个服务和 API 级别摘要。  
-   在每个小时之内，如果服务利用在服务中，每个 API，则大约 12 KB 的数据存储在度量值事务表中每隔一小时，如果已启用只是服务级别摘要。  
-   Blob 的容量表有两个行添加每一天，提供您已选择入日志。 这意味着，每一天，此表的大小由最多增加约 300 字节。

## <a name="next-steps"></a>后续步骤
* [如何监视存储帐户](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema.md)   
* [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md)   
* [存储分析日志记录](storage-analytics-logging.md)
