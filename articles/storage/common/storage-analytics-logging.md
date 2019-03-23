---
title: Azure 存储分析日志记录
description: 了解如何记录有关对 Azure 存储发出的请求的详细信息。
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: a350576742a9bcb899405aae19c032cc9b966975
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351314"
---
# <a name="azure-storage-analytics-logging"></a>Azure 存储分析日志记录

存储分析记录成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。

 默认情况下，存储帐户的情况下不启用存储分析日志记录。 可以在 [Azure 门户](https://portal.azure.com/)中启用它；有关详细信息，请参阅[在 Azure 门户中监视存储帐户](/azure/storage/storage-monitor-storage-account)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用[获取 Blob 服务属性](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)，[获取队列服务属性](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)，并[获取表服务属性](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties)操作为每个服务启用存储分析。

 仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果，存储帐户中 Blob 终结点，但不是在其表或队列终结点的活动，将创建仅与 Blob 服务有关的日志。

> [!NOTE]
>  存储分析日志记录是目前仅适用于 Blob、 队列和表服务。 但是，不支持高级存储帐户。

## <a name="requests-logged-in-logging"></a>在日志记录中记录的请求

### <a name="logging-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的已经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用共享访问签名 (SAS) 或 OAuth，包括失败和成功请求的请求
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

所有日志都存储在名为的容器的块 blob `$logs`，它在存储帐户启用存储分析时自动创建。 `$logs`容器位于存储帐户的 blob 命名空间中，例如： `http://<accountname>.blob.core.windows.net/$logs`。 在启用存储分析后，无法删除该容器，但可以删除其内容。 如果使用存储浏览工具直接导航到的容器，你将看到包含日志记录数据的所有 blob。

> [!NOTE]
>  `$logs`执行容器列出操作，如列出容器操作时不会显示容器。 必须直接访问该容器。 例如，可以使用列出的 Blob 操作访问中的 blob`$logs`容器。

在记录请求时，存储分析将中间结果作为块进行上传。 存储分析定期提交这些块，并将其作为 Blob 提供。 它可能需要一小时才会出现在 blob 中的日志数据的时间长达 **$logs**容器因为存储服务会刷新日志编写器的频率。 在同一小时内创建的日志中可能存在重复的记录。 可以通过检查 RequestId 和操作编号确定记录是否为重复记录。

如果必须使用多个文件的日志数据量很高的每个小时，然后可以使用 blob 元数据来确定该日志包含通过检查 blob 元数据字段的数据。 因为有时可能存在延迟的日志文件写入数据时，这是也很有用： blob 元数据提供更准确指示 blob 内容的 blob 名称。

大多数存储浏览工具，您可以查看的 blob; 元数据您还可以阅读此信息使用 PowerShell 或以编程方式。 以下 PowerShell 代码片段是一种筛选日志 blob 的列表，按名称指定的时间和元数据来标识这些日志包含由**编写**操作。  

 ```  
 Get-AzureStorageBlob -Container '$logs' |  
 where {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 foreach {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

有关以编程方式列出 blob 的信息，请参阅[枚举 Blob 资源](http://msdn.microsoft.com/library/azure/hh452233.aspx)并[设置和检索属性和元数据的 Blob 资源](http://msdn.microsoft.com/library/azure/dd179404.aspx)。  

### <a name="log-naming-conventions"></a>日志命名约定

 每个日志将写入采用以下格式：

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 下表说明了日志名称中的每个属性：

|属性|描述|
|---------------|-----------------|
|`<service-name>`|存储服务的名称 例如： `blob`， `table`，或 `queue`|
|`YYYY`|在日志四位数年份。 例如： `2011`|
|`MM`|两位数的月份的日志。 例如： `07`|
|`DD`|两位数的日期的日志。 例如： `31`|
|`hh`|两位数表示的日志起始小时采用 24 小时 UTC 格式。 例如： `18`|
|`mm`|两位数表示的日志起始分钟。 **注意：** 此值当前版本的存储分析中不受支持，其值将始终为`00`。|
|`<counter>`|从零开始且具有六位数字的计数器，表示在 1 小时内为存储服务生成的日志 Blob 数。 此计数器从开始`000000`。 例如： `000001`|

 下面是组合上述示例的完整示例日志名称：

 `blob/2011/07/31/1800/000001.log`

 下面是示例可用于访问上述日志的 URI:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 在记录存储请求时，生成的日志名称与完成请求的操作时间（小时）关联。 例如，如果在 2011 年 7 月 31 日下午 6:30 完成 GetBlob 请求，将具有以下前缀写入的日志： `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>日志元数据

 所有日志 Blob 与可用于确定 Blob 包含哪些日志记录数据的元数据一起存储。 下表说明了每个元数据属性：

|属性|描述|
|---------------|-----------------|
|`LogType`|描述日志是否包含与读取、写入或删除操作有关的信息。 该值可能包含一种类型，也可能包含所有三种类型的组合并用逗号隔开。<br /><br /> 示例 1：`write`<br /><br /> 示例 2：`read,write`<br /><br /> 示例 3: `read,write,delete`|
|`StartTime`|中的窗体的日志中的条目的最早时间`YYYY-MM-DDThh:mm:ssZ`。 例如： `2011-07-31T18:21:46Z`|
|`EndTime`|中的窗体的日志中的条目的最晚时间`YYYY-MM-DDThh:mm:ssZ`。 例如： `2011-07-31T18:22:09Z`|
|`LogVersion`|日志格式的版本。|

 下表显示了使用上述示例的完整示例元数据：

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>启用存储日志记录

您可以使用 Azure 门户、 PowerShell 和存储 Sdk 存储日志记录。

### <a name="enable-storage-logging-using-the-azure-portal"></a>使用 Azure 门户启用存储日志记录  

在 Azure 门户中，使用**诊断设置 （经典）** 边栏选项卡中存储的日志记录，可从访问控制**监视 （经典）** 部分中的存储帐户的**菜单边栏选项卡**.

您可以指定想要登录，存储服务和记录的数据的保留期 （以天为单位）。  

### <a name="enable-storage-logging-using-powershell"></a>使用 PowerShell 启用存储日志记录  

 可以在本地计算机上使用 PowerShell 配置存储日志记录在存储帐户中使用 Azure PowerShell cmdlet **Get-azurestorageserviceloggingproperty**检索当前设置，然后 cmdlet **Set-azurestorageserviceloggingproperty**若要更改当前设置。  

 控制存储日志记录的 cmdlet 使用**LoggingOperations**是一个包含一列以逗号分隔的要记录的请求类型字符串的参数。 三种可能的请求类型是**读取**，**编写**，并**删除**。 若要关闭日志记录，请使用值**无**有关**LoggingOperations**参数。  

 以下命令切换为读取日志记录、 写入和删除在默认存储帐户中的队列服务中的请求设为 5 天的保留期：  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 以下命令会关闭在默认存储帐户中的表服务的日志记录：  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 若要了解如何配置 Azure PowerShell cmdlet 来使用 Azure 订阅并了解如何选择要使用的默认存储帐户，请参阅：[如何安装和配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)。  

### <a name="enable-storage-logging-programmatically"></a>启用存储日志记录以编程方式  
 除了使用 Azure 门户或 Azure PowerShell cmdlet 控制存储日志记录，你还可以使用 Azure 存储 Api 之一。 例如，如果使用的一种.NET 语言可以使用存储客户端库。  

 类**CloudBlobClient**， **CloudQueueClient**，并**CloudTableClient**所有具有方法，例如**SetServiceProperties**并**SetServicePropertiesAsync**采用**ServiceProperties**对象作为参数。 可以使用**ServiceProperties**对象配置存储日志记录。 例如，以下C#代码片段演示如何更改记录的内容和队列日志记录的保留期：  

```  
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 有关使用.NET 语言配置存储日志记录的详细信息，请参阅[存储客户端库参考](https://msdn.microsoft.com/library/azure/dn261237.aspx)。  

 有关配置存储日志记录使用 REST API 的常规信息，请参阅[启用和配置存储分析](https://msdn.microsoft.com/library/azure/hh360996.aspx)。  

## <a name="download-storage-logging-log-data"></a>下载存储日志记录日志数据

 若要查看和分析日志数据，应下载包含你感兴趣到本地计算机的日志数据的 blob。 很多存储浏览工具，可从你的存储帐户; 下载 blob你还可以使用 Azure 存储团队提供命令行 Azure 复制工具 (**AzCopy**) 若要下载日志数据。  

 若要确保下载你感兴趣的日志数据，以避免多次下载相同的日志数据：  

-   使用日期和时间命名约定包含日志数据，用于跟踪用 blob 的 blob 已下载的分析，以避免多次重新下载相同的数据。  

-   使用上包含日志数据的 blob 元数据来标识特定的时间段，blob 会为其保留日志数据以标识您需要下载的确切 blob。  

> [!NOTE]
>  AzCopy 是 Azure SDK 的一部分，但始终可以在下载中的最新版本[ https://aka.ms/AzCopy ](https://aka.ms/AzCopy)。 默认情况下，AzCopy 安装在文件夹**C:\Program Files (x86) \Microsoft SDKs\Windows Azure\AzCopy**，并应将此文件夹添加到你的路径中，再尝试在命令提示符或 PowerShell 窗口中运行该工具。  

 下面的示例演示如何下载 09 AM、 AM，10 和 20，2014 年 11 点开始小时的队列服务的日志数据。 **/S**参数使 AzCopy 可以生成基于日期和时间在日志文件名称; 中的本地文件夹结构 **/V**参数使 AzCopy 以生成详细输出; **/Y**参数会导致覆盖任何本地文件的 AzCopy。 替换 **< yourstorageaccount\>** 同名的存储帐户和替换 **< yourstoragekey\>** 与你的存储帐户密钥。  

```  
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy 还有一些有用的参数控制它如何在下载的文件设置的上次修改时间以及是否它将尝试下载文件更旧或更新比在本地计算机已存在的任何文件。 您还可以在可重新启动模式下运行它。 有关完整详细信息，查看帮助通过运行**AzCopy /？** 命令。  

 有关如何以编程方式下载日志数据的示例，请参阅博客文章[Windows Azure 存储日志记录：使用日志跟踪存储请求](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx)并搜索"dumplogs 一"词的页上。  

 下载日志数据后，可以在文件中查看日志条目。 这些日志文件使用带分隔符的文本格式，许多日志读取工具可分析，包括 Microsoft Message Analyzer (有关详细信息，请参阅指南[进行监视、 诊断和故障排除 Microsoft Azure 存储](storage-monitoring-diagnosing-troubleshooting.md)). 不同的工具使用不同设施的格式设置、 筛选、 排序和搜索日志文件的内容。 有关存储日志记录日志文件格式和内容的详细信息，请参阅[存储分析日志格式](/rest/api/storageservices/storage-analytics-log-format)并[存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。

## <a name="next-steps"></a>后续步骤
* [Storage Analytics Log Format](/rest/api/storageservices/storage-analytics-log-format)（存储分析日志格式）
* [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [存储分析指标 （经典）](storage-analytics-metrics.md)
