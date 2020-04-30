---
title: Azure 存储分析日志记录
description: 了解如何记录有关针对 Azure 存储发出的请求的详细信息。
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 1e41eb02f4b02078dbf4d42c46cab574cf8d0701
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204060"
---
# <a name="azure-storage-analytics-logging"></a>Azure 存储分析日志记录

存储分析记录成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。

 默认不会对存储帐户启用存储分析日志记录。 可以在 [Azure 门户](https://portal.azure.com/)中启用它；有关详细信息，请参阅[在 Azure 门户中监视存储帐户](/azure/storage/storage-monitor-storage-account)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用[获取 Blob 服务属性](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)、[获取队列服务属性](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)和[获取表服务属性](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties)操作为每个服务启用存储分析。

 仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户的 Blob 终结点中存在活动，而表或队列终结点中没有该活动，则仅创建与 Blob 服务有关的日志。

> [!NOTE]
>  存储分析日志记录目前仅适用于 Blob、队列和表服务。 但是，不支持高级存储帐户。

## <a name="requests-logged-in-logging"></a>在日志记录中记录的请求
### <a name="logging-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用共享访问签名 (SAS) 或 OAuth 的请求，包括失败和成功的请求。
- 分析数据请求

  不会记录存储分析本身发出的请求，如创建或删除日志。 [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。

### <a name="logging-anonymous-requests"></a>记录匿名请求

 记录以下类型的匿名请求：

- 成功的请求
- 服务器错误
- 客户端和服务器的超时错误
- 失败的 GET 请求，错误代码为 304（未修改）

  不会记录所有其他失败的匿名请求。 [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。

## <a name="how-logs-are-stored"></a>如何存储日志

所有日志以块 Blob 的形式存储在一个名为 `$logs` 的容器中，为存储帐户启用存储分析时自动创建该容器。 `$logs` 容器位于存储帐户的 Blob 命名空间中，例如：`http://<accountname>.blob.core.windows.net/$logs`。 启用存储分析后无法删除该容器，但可以删除其内容。 如果使用存储浏览工具直接导航到容器，则会看到包含日志记录数据的所有 Blob。

> [!NOTE]
>  执行容器列出操作（例如“列出容器”操作）时，不会显示 `$logs` 容器。 必须直接访问该容器。 例如，可以使用“列出 Blob”操作访问 `$logs` 容器中的 Blob。

记录请求时，存储分析以块形式上传中间结果。 存储分析会定期提交这些块，并将其作为 Blob 提供。 最长可能需要在一小时后，日志才会显示在 **$logs** 容器中的 Blob 内，具体时间取决于存储服务刷新日志写入器的频率。 在同一小时内创建的日志中可能存在重复的记录。 可以通过检查 RequestId 和操作编号确定记录是否为重复记录。  

如果每小时写入大量的日志数据和多个文件，则你可以使用 Blob 元数据并通过检查 Blob 元数据字段来确定日志包含哪些数据。 这种做法也很有效，因为将数据写入日志文件时，有时会出现延迟：与 Blob 名称相比，Blob 元数据能够更准确地指示 Blob 内容。

可以使用大部分存储浏览工具来查看 Blob 的元数据；还可以使用 PowerShell 或以编程方式阅读此信息。 以下 PowerShell 示例代码片段通过指定时间并按名称筛选日志 Blob 列表，并按元数据标识仅包含 **write** 操作的日志。  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

有关以编程方式列出 Blob 的信息，请参阅[枚举 Blob 资源](https://msdn.microsoft.com/library/azure/hh452233.aspx)以及[设置和检索 Blob 资源的属性与元数据](https://msdn.microsoft.com/library/azure/dd179404.aspx)。  

### <a name="log-naming-conventions"></a>日志命名约定

 每条日志按以下格式写入：

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 下表描述了日志名称中的每个属性：

|属性|说明|
|---------------|-----------------|
|`<service-name>`|存储服务的名称。 例如：`blob`、`table` 或 `queue`|
|`YYYY`|用四位数表示的日志年份。 例如： `2011`|
|`MM`|用两位数表示的日志月份。 例如： `07`|
|`DD`|用两位数表示的日志日期。 例如： `31`|
|`hh`|用两位数表示的日志起始小时，采用 24 小时 UTC 格式。 例如： `18`|
|`mm`|用两位数表示的日志起始分钟。 **注意：** 最新版本的存储分析不支持此值，其值始终为 `00`。|
|`<counter>`|从零开始的计数器，用六位数字表示 1 小时内为存储服务生成的日志 Blob 数。 此计数器从 `000000` 开始。 例如： `000001`|

 下面是合并了前述示例的完整示例日志名称：

 `blob/2011/07/31/1800/000001.log`

 下面是可用于访问上述日志的示例 URI：

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 记录存储请求时，生成的日志名称与完成请求的操作时间（小时）关联。 例如，如果是在 2011 年 7 月 31 日下午 6:30 完成了 GetBlob 请求，则会写入带有以下前缀的日志：`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>日志元数据

 所有日志 Blob 与可用于确定 Blob 包含哪些日志记录数据的元数据一起存储。 下表描述了每个元数据属性：

|属性|说明|
|---------------|-----------------|
|`LogType`|描述日志是否包含与读取、写入或删除操作有关的信息。 该值可能包含一种类型，也可能包含所有三种类型的组合并用逗号隔开。<br /><br /> 示例 1：`write`<br /><br /> 示例 2：`read,write`<br /><br /> 示例 3：`read,write,delete`|
|`StartTime`|日志中条目的最早时间，采用 `YYYY-MM-DDThh:mm:ssZ` 格式。 例如： `2011-07-31T18:21:46Z`|
|`EndTime`|日志中条目的最晚时间，采用 `YYYY-MM-DDThh:mm:ssZ` 格式。 例如： `2011-07-31T18:22:09Z`|
|`LogVersion`|日志格式的版本。|

 以下列表显示了使用前述示例的完整示例元数据：

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>启用存储日志记录

可以使用 Azure 门户、PowerShell 和存储 SDK 启用存储日志记录。

### <a name="enable-storage-logging-using-the-azure-portal"></a>使用 Azure 门户启用存储日志记录  

在 Azure 门户中，使用“诊断设置(经典)”边栏选项卡控制存储日志记录。可以通过存储帐户的“菜单”边栏选项卡的“监视(经典)”部分访问该项设置。   

可以指定要记录的存储服务，以及记录数据的保留期（以天为单位）。  

### <a name="enable-storage-logging-using-powershell"></a>使用 PowerShell 启用存储日志记录  

 可在本地计算机上使用 PowerShell 在存储帐户中配置存储日志记录，方法是：使用 Azure PowerShell cmdlet **Get-AzureStorageServiceLoggingProperty** 检索当前设置，使用 cmdlet **Set-AzureStorageServiceLoggingProperty** 更改当前设置。  

 用于控制存储日志记录的 cmdlet 使用 **LoggingOperations** 参数，该参数是一个字符串，包含要记录的请求类型的逗号分隔列表。 三个可能的请求类型为 **read**、**write** 和 **delete**。 若要禁用日志记录，请对 **LoggingOperations** 参数使用值 **none**。  

 以下命令针对默认存储帐户中的队列服务内的 read、write 和 delete 请求启用日志记录，并将日志保留期设置为 5 天：  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 以下命令针对默认存储帐户中的表服务禁用日志记录：  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 若要了解如何配置 Azure PowerShell cmdlet 来使用 Azure 订阅并了解如何选择要使用的默认存储帐户，请参阅：[如何安装和配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

### <a name="enable-storage-logging-programmatically"></a>以编程方式启用存储日志记录  

 除了使用 Azure 门户或 Azure PowerShell cmdlet 来控制存储日志记录以外，还可以使用某个 Azure 存储 API 来实现此目的。 例如，如果使用的是 .NET 语言，则可以使用存储客户端库。  

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

# <a name="net-v11-sdk"></a>[\.网络 v11 SDK](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

---


 有关使用 .NET 语言配置存储日志记录的详细信息，请参阅[存储客户端库参考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。  

 有关使用 REST API 配置存储日志记录的一般信息，请参阅[启用和配置存储分析](https://msdn.microsoft.com/library/azure/hh360996.aspx)。  

## <a name="download-storage-logging-log-data"></a>下载存储日志记录日志数据

 若要查看和分析日志数据，应将包含所需日志数据的 Blob 下载到本地计算机。 可以使用许多存储浏览工具从存储帐户下载 Blob；还可以使用 Azure 存储团队提供的命令行 Azure 复制工具 ([AzCopy](storage-use-azcopy-v10.md)) 下载日志数据。  
 
>[!NOTE]
> 此`$logs`容器未与事件网格集成，因此写入日志文件时不会收到通知。 

 为了确保下载所需的日志数据，并避免多次下载相同的日志数据：  

-   请使用包含日志数据的 Blob 的日期和时间命名约定来跟踪已下载进行分析的 Blob，并避免多次重新下载相同的数据。  

-   使用包含日志数据的 Blob 中的元数据来识别该 Blob 保存日志数据的特定时限，以识别需要下载的确切 Blob。  

若要开始使用 AzCopy，请参阅 [AzCopy 入门](storage-use-azcopy-v10.md) 

以下示例演示如何下载队列服务的日志数据，这些数据的起始时间为 2014 年 5 月 20 日上午 9 点、10 点和 11 点。

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

若要详细了解如何下载特定文件，请参阅[下载特定文件](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files)。

下载日志数据后，可以查看文件中的日志条目。 这些日志文件使用一种分隔文本格式，许多日志读取工具都可以对其进行分析，包括 Microsoft Message Analyzer （有关详细信息，请参阅指南[监视、诊断和疑难解答 Microsoft Azure 存储](storage-monitoring-diagnosing-troubleshooting.md)）。 不同的工具提供不同的功能用于筛选、排序和搜索日志文件的内容及设置其格式。 有关存储日志记录日志文件格式和内容的详细信息，请参阅[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)以及[存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。

## <a name="next-steps"></a>后续步骤

* [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format)（存储分析日志格式）
* [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [存储分析指标（经典）](storage-analytics-metrics.md)
