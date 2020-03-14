---
title: Azure 存储分析日志记录
description: 了解如何记录有关对 Azure 存储发出的请求的详细信息。
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 25c047dc9b2ce08ca39e69c6f106e41c5d9bd0dc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268413"
---
# <a name="azure-storage-analytics-logging"></a>Azure 存储分析日志记录

存储分析记录成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。

 默认未对存储帐户启用存储分析日志记录。 可以在 [Azure 门户](https://portal.azure.com/)中启用它；有关详细信息，请参阅[在 Azure 门户中监视存储帐户](/azure/storage/storage-monitor-storage-account)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用[获取 Blob 服务属性](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)、[获取队列服务属性](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)和[获取表服务属性](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties)操作，为每个服务启用存储分析。

 仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户的 Blob 终结点中存在活动，而在其表或队列终结点中没有活动，则仅创建与 Blob 服务有关的日志。

> [!NOTE]
>  存储分析日志记录现仅可用于 Blob、队列和表服务。 但是，不支持高级存储帐户。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>记录中记录的请求
### <a name="logging-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的已经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用共享访问签名（SAS）或 OAuth 的请求，包括失败和成功的请求
- 分析数据请求

  不会记录存储分析本身发出的请求，如创建或删除日志。 [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。

### <a name="logging-anonymous-requests"></a>记录匿名请求

 将记录以下类型的匿名请求：

- 成功的请求
- 服务器错误
- 客户端和服务器的超时错误
- 失败的 GET 请求，错误代码为 304（未修改）

  不会记录所有其他失败的匿名请求。 [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。

## <a name="how-logs-are-stored"></a>如何存储日志

所有日志以块 Blob 的形式存储在一个名为 `$logs` 的容器中，为存储帐户启用存储分析时将自动创建该容器。 `$logs` 容器位于存储帐户的 Blob 命名空间中，例如：`http://<accountname>.blob.core.windows.net/$logs`。 在启用存储分析后，无法删除该容器，但可以删除其内容。 如果使用存储浏览工具直接导航到容器，则会看到包含日志记录数据的所有 blob。

> [!NOTE]
>  执行容器列出操作（例如列表容器操作）时，不会显示 `$logs` 容器。 必须直接访问该容器。 例如，可以使用 "列出 Blob" 操作访问 `$logs` 容器中的 blob。

在记录请求时，存储分析将中间结果作为块进行上传。 存储分析定期提交这些块，并将其作为 Blob 提供。 日志数据会出现在 **$logs**容器中的 blob 中最多需要一小时，因为存储服务刷新日志编写器的频率。 在同一小时内创建的日志中可能存在重复的记录。 可以通过检查 RequestId 和操作编号确定记录是否为重复记录。

如果你每小时都有包含多个文件的大量日志数据，则可检查 Blob 元数据字段，以便使用 Blob 元数据确定日志中包含的数据。 这也很有用，因为在将数据写入日志文件时有时可能会有延迟： blob 元数据提供的 blob 内容比 blob 名称更准确。

你可以使用大多数存储浏览工具查看 Blob 的元数据；你也可以通过 PowerShell 或编程方式读取此信息。 下面的 PowerShell 代码段是一个示例，该示例说明了如何按名称筛选日志 Blob 的列表来指定时间，以及如何按元数据进行筛选以仅标识包含 **write** 操作的那些日志。  

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

有关以编程方式列出 blob 的详细信息，请参阅[枚举 Blob 资源](https://msdn.microsoft.com/library/azure/hh452233.aspx)和[设置和检索 Blob 资源的属性和元数据](https://msdn.microsoft.com/library/azure/dd179404.aspx)。  

### <a name="log-naming-conventions"></a>日志命名约定

 每个日志是使用以下格式写入的：

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 下表说明了日志名称中的每个属性：

|Attribute|说明|
|---------------|-----------------|
|`<service-name>`|存储服务的名称 例如：`blob`、`table` 或 `queue`|
|`YYYY`|用四位数表示的日志年份。 例如： `2011`|
|`MM`|用两位数表示的日志月份。 例如： `07`|
|`DD`|用两位数表示的日志日期。 例如： `31`|
|`hh`|用两位数表示的日志起始小时，采用 24 小时 UTC 格式。 例如： `18`|
|`mm`|用两位数表示的日志起始分钟。 **注意：** 此值在存储分析的当前版本中不受支持，并且将始终 `00`其值。|
|`<counter>`|从零开始且具有六位数字的计数器，表示在 1 小时内为存储服务生成的日志 Blob 数。 此计数器从 `000000` 开始。 例如： `000001`|

 下面是组合上述示例的完整示例日志名称：

 `blob/2011/07/31/1800/000001.log`

 下面是一个可用于访问上述日志的示例 URI：

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 在记录存储请求时，生成的日志名称与完成请求的操作时间（小时）关联。 例如，如果 GetBlob 请求已在7/31/2011 的6：下午6:30 上完成，则将使用以下前缀来写入日志： `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>日志元数据

 所有日志 Blob 与可用于确定 Blob 包含哪些日志记录数据的元数据一起存储。 下表说明了每个元数据属性：

|Attribute|说明|
|---------------|-----------------|
|`LogType`|描述日志是否包含与读取、写入或删除操作有关的信息。 该值可能包含一种类型，也可能包含所有三种类型的组合并用逗号隔开。<br /><br /> 示例 1：`write`<br /><br /> 示例 2：`read,write`<br /><br /> 示例3： `read,write,delete`|
|`StartTime`|日志中的项的最早时间，采用 `YYYY-MM-DDThh:mm:ssZ` 形式。 例如： `2011-07-31T18:21:46Z`|
|`EndTime`|日志中的项的最晚时间，采用 `YYYY-MM-DDThh:mm:ssZ` 形式。 例如： `2011-07-31T18:22:09Z`|
|`LogVersion`|日志格式的版本。|

 下表显示了使用上述示例的完整示例元数据：

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>启用存储日志记录

可以使用 Azure 门户、PowerShell 和存储 Sdk 来启用存储日志记录。

### <a name="enable-storage-logging-using-the-azure-portal"></a>使用 Azure 门户启用存储日志记录  

在 Azure 门户中，使用 "**诊断设置（经典）** " 边栏选项卡控制存储日志记录，可从存储帐户的**菜单边栏选项卡**的 "**监视（经典）** " 部分进行访问。

可以指定要记录的存储服务，以及记录的数据的保持期（天）。  

### <a name="enable-storage-logging-using-powershell"></a>使用 PowerShell 启用存储日志记录  

 你可以使用本地计算机上的 PowerShell 在存储帐户中配置存储日志记录，具体方法是：使用 Azure PowerShell cmdlet **Get-AzureStorageServiceLoggingProperty** 检索当前设置，然后使用 cmdlet **Set-AzureStorageServiceLoggingProperty** 更改当前设置。  

 控制存储日志记录的 cmdlet 使用 **LoggingOperations** 参数，该参数是包含要记录的、以逗号分隔的请求类型列表的字符串。 三种可能的请求类型是 **“读取”** 、 **“写入”** 和 **“删除”** 。 若要关闭日志记录，请对 **LoggingOperations** 参数使用值 **“无”** 。  

 以下命令在保留设置为5天的情况下，在默认存储帐户的队列服务中打开读取、写入和删除请求的日志记录：  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 以下命令在默认存储帐户中为表服务关闭日志记录：  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 若要了解如何配置 Azure PowerShell cmdlet 来使用 Azure 订阅并了解如何选择要使用的默认存储帐户，请参阅：[如何安装和配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

### <a name="enable-storage-logging-programmatically"></a>以编程方式启用存储日志记录  

 除了使用 Azure 门户或 Azure PowerShell cmdlet 控制存储日志记录之外，还可以使用 Azure 存储 Api 之一。 例如，如果你要使用 .NET 语言，则可以使用存储客户端库。  

 **CloudBlobClient**、**CloudQueueClient** 和 **CloudTableClient** 类都使用 **SetServiceProperties** 和 **SetServicePropertiesAsync** 等方法，这些方法采用 **ServiceProperties** 对象作为参数。 你可以使用 **ServiceProperties** 对象配置存储日志记录。 例如，下面的 C# 代码段说明如何更改记录的内容，以及队列日志记录的保留期：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 有关使用 .NET 语言配置存储日志记录的详细信息，请参阅[存储客户端库参考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。  

 有关使用 REST API 配置存储日志记录的常规信息，请参阅[启用和配置存储分析](https://msdn.microsoft.com/library/azure/hh360996.aspx)。  

## <a name="download-storage-logging-log-data"></a>下载存储日志记录日志数据

 若要查看和分析日志数据，你应该将包含你感兴趣的日志数据的 Blob 下载到本地计算机。 许多存储浏览工具可让你从存储帐户下载 blob;你还可以使用 Azure 存储团队提供的命令行 Azure 复制工具[AzCopy](storage-use-azcopy-v10.md)下载日志数据。  
 
>[!NOTE]
> `$logs` 容器未与事件网格集成，因此写入日志文件时不会收到通知。 

 若要确保下载你感兴趣的日志数据，并避免多次下载相同的日志数据，请执行以下操作：  

-   对包含日志数据的 Blob 使用日期和时间命名约定，以跟踪已下载的分析用 Blob，从而避免多次重新下载相同的数据。  

-   使用包含日志数据的 Blob 中的元数据来确定特定期限，在该期限内，Blob 会保留日志数据以标识需要下载的确切 Blob。  

若要开始 AzCopy，请参阅[AzCopy 入门](storage-use-azcopy-v10.md) 

下面的示例演示了在2014可能的情况下，如何从第09点、上午10点到11点上午11点开始，下载队列服务的日志数据。

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

若要了解有关如何下载特定文件的详细信息，请参阅[下载特定文件](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files)。

下载了日志数据后，你可以在文件中查看日志条目。 这些日志文件使用一种分隔文本格式，许多日志读取工具都可以对其进行分析，包括 Microsoft Message Analyzer （有关详细信息，请参阅指南[监视、诊断和疑难解答 Microsoft Azure 存储](storage-monitoring-diagnosing-troubleshooting.md)）。 不同工具使用不同设施对日志文件内容进行格式化、筛选、排序和搜索。 有关存储日志记录日志文件格式和内容的详细信息，请参阅[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)和[存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。

## <a name="next-steps"></a>后续步骤

* [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format)（存储分析日志格式）
* [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [存储分析指标（经典）](storage-analytics-metrics.md)
