---
title: 排查 Azure 数据工厂问题 |Microsoft Docs
description: 了解如何对 Azure 数据工厂中的外部控制活动进行故障排除。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 0026aa377a58f6b766a400860692a35440deb962
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748359"
---
# <a name="troubleshoot-azure-data-factory"></a>排查 Azure 数据工厂问题

本文探讨了 Azure 数据工厂中的外部控制活动的常见故障排除方法。

## <a name="connector-and-copy-activity"></a>连接器和复制活动

有关连接器问题（例如，使用复制活动时遇到错误），请参阅对[Azure 数据工厂连接器进行故障排除](connector-troubleshoot-guide.md)。
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>错误代码：3200

- **消息**：错误403。

- **原因**： `The Databricks access token has expired.`

- **建议**：默认情况下，Azure Databricks 访问令牌的有效期为90天。 创建新的令牌并更新链接的服务。


### <a name="error-code--3201"></a>错误代码：3201

- **消息**： `Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**： `Bad authoring: Notebook path not specified correctly.`

- **建议**：在 Databricks 活动中指定笔记本路径。

<br/>    
              
- **消息**： `Cluster   ... does not exist.`

- **原因**： `Authoring error: Databricks cluster does not exist or has been deleted.`

- **建议**：验证 Databricks 群集是否存在。

<br/>  

- **消息**： `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **原因**： `Bad authoring.`

- **建议**：为工作区寻址方案指定绝对路径，或者为存储在 Databricks 文件系统中的文件指定 `dbfs:/folder/subfolder/foo.py`。

<br/>  

- **消息**： `{0} LinkedService should have domain and accessToken as required properties.`

- **原因**： `Bad authoring.`

- **建议**：验证[链接的服务定义](compute-linked-services.md#azure-databricks-linked-service)。

<br/>  

- **消息**： `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**： `Bad authoring.`

- **建议**：验证[链接的服务定义](compute-linked-services.md#azure-databricks-linked-service)。

<br/>  

- **消息**： `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **原因**： `Bad authoring.`

- **建议**：请参阅错误消息。

<br/>

### <a name="error-code--3202"></a>错误代码：3202

- **消息**： `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **原因**： `Too many Databricks runs in an hour.`

- **建议**：检查使用此 Databricks 工作区的所有管道的作业创建速率。  如果管道启动了太多的 Databricks，则将一些管道迁移到新的工作区。

<br/>  

- **消息**： `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**： `Authoring error: No value provided for the parameter.`

- **建议**：检查管道 JSON 并确保 baseParameters 笔记本中的所有参数都指定一个非空值。

<br/>  

- **消息**： `User: `SimpleUserContext {userId = ...，name =user@company.com，orgId = ...}` is not   authorized to access cluster.`

- **原因**：不允许生成访问令牌的用户访问在链接服务中指定的 Databricks 群集。

- **建议**：确保用户在工作区中具有所需的权限。


### <a name="error-code--3203"></a>错误代码：3203

- **消息**： `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**：群集已终止。 对于交互式群集，这可能是一个争用情况。

- **建议**：避免此错误的最佳方式是使用作业群集。


### <a name="error-code--3204"></a>错误代码：3204

- **消息**： `Job execution failed.`

- **原因**：错误消息指示各种问题，例如意外群集状态或特定活动。 通常不会显示任何错误消息。

- **建议**：不适用
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表适用于 U SQL。
      
### <a name="error-code--2709"></a>错误代码：2709

- **消息**： `The access token is from the wrong tenant.`

- **原因**：不正确的 Azure Active Directory （Azure AD）租户。

- **建议**：不正确的 Azure Active Directory （Azure AD）租户。

<br/>

- **消息**： `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **原因**：此错误是由 Data Lake Analytics 上的限制引起的。

- **建议**：更改活动的数据工厂触发器和并发设置，将提交的作业数减少到 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。

<br/>  

- **消息**： `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**：此错误是由 Data Lake Analytics 上的限制引起的。

- **建议**：更改活动的数据工厂触发器和并发设置，将提交的作业数减少到 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。


### <a name="error-code--2705"></a>错误代码：2705

- **消息**： `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：确保用户为 Data Lake Analytics 作业提供的服务主体或证书可以访问根文件夹中的 Data Lake Analytics 帐户和默认 Data Lake Storage 实例。


### <a name="error-code--2711"></a>错误代码：2711

- **消息**： `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：确保用户为 Data Lake Analytics 作业提供的服务主体或证书可以访问根文件夹中的 Data Lake Analytics 帐户和默认 Data Lake Storage 实例。

<br/>  

- **消息**： `Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**： U SQL 文件的路径错误，或链接的服务凭据没有访问权限。

- **建议**：验证在链接服务中提供的路径和凭据。


### <a name="error-code--2704"></a>错误代码：2704

- **消息**： `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：确保用户为 Data Lake Analytics 作业提供的服务主体或证书可以访问根文件夹中的 Data Lake Analytics 帐户和默认 Data Lake Storage 实例。


### <a name="error-code--2707"></a>错误代码：2707

- **消息**： `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **原因**：链接的服务中的 Data Lake Analytics 帐户错误。

- **建议**：验证是否提供了正确的帐户。


### <a name="error-code--2703"></a>错误代码：2703

- **消息**： `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **原因**：错误来自 Data Lake Analytics。

- **建议**：类似于示例的错误表示作业已提交到 Data Lake Analytics，脚本出现故障。 在 Data Lake Analytics 中进行调查。 在门户中，中转到 Data Lake Analytics 帐户，并使用数据工厂活动运行 ID （不是管道运行 ID）查找作业。 其中提供了有关错误的详细信息，并将帮助你进行故障排除。 如果解决方法不清楚，请联系 Data Lake Analytics 支持团队并提供作业 URL，其中包括你的帐户名称和作业 ID。
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>错误代码：3602

- **消息**： `Invalid HttpMethod: '%method;'.`

- **原因**： Azure Function 活动不支持活动有效负载中指定的 Http 方法。

- **建议**：支持的 Http 方法有 PUT、POST、GET、DELETE、OPTIONS、HEAD 和 TRACE。


### <a name="error-code--3603"></a>错误代码：3603

- **消息**： `Response Content is not a valid JObject.`

- **原因**：调用的 Azure 函数未在响应中返回 JSON 有效负载。 ADF Azure 函数活动仅支持 JSON 响应内容。

- **建议**：更新 Azure function 以返回有效的 JSON 有效负载，例如C# ，函数可能返回（ActionResult） new OkObjectResult （"{\"Id\"：\"123\"}"）;


### <a name="error-code--3606"></a>错误代码：3606

- **消息**： Azure function 活动缺少功能键。

- **原因**： Azure function 活动定义不完整。

- **建议**：请检查输入 AZUREFUNCTION 活动 JSON 定义是否包含名为 "functionKey" 的属性。


### <a name="error-code--3607"></a>错误代码：3607

- **消息**： `Azure function activity missing function name.`

- **原因**： Azure function 活动定义不完整。

- **建议**：请检查输入 AZUREFUNCTION 活动 JSON 定义是否包含名为 "functionName" 的属性。


### <a name="error-code--3608"></a>错误代码：3608

- **消息**： `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **原因**：活动定义中的 Azure function 详细信息可能不正确。

- **建议**：修复 azure function 详细信息，然后重试。


### <a name="error-code--3609"></a>错误代码：3609

- **消息**： `Azure function activity missing functionAppUrl.`

- **原因**： Azure function 活动定义不完整。

- **建议**：请检查输入 AZUREFUNCTION 活动 JSON 定义是否包含名为 "functionAppUrl" 的属性。


### <a name="error-code--3610"></a>错误代码：3610

- **消息**： `There was an error while calling endpoint.`

- **原因**：函数 URL 可能不正确。

- **建议**：请确保活动 JSON 中 "functionAppUrl" 的值正确，然后重试。


### <a name="error-code--3611"></a>错误代码：3611

- **消息**： `Azure function activity missing Method in JSON.`

- **原因**： Azure function 活动定义不完整。

- **建议**：请检查输入 AZUREFUNCTION 活动 JSON 定义是否具有名为 "method" 的属性。


### <a name="error-code--3612"></a>错误代码：3612

- **消息**： `Azure function activity missing LinkedService definition in JSON.`

- **原因**： Azure function 活动定义不完整。

- **建议**：请检查输入 AZUREFUNCTION 活动 JSON 定义是否有链接的服务详细信息。



## <a name="azure-machine-learning"></a>Azure 机器学习

### <a name="error-code--4101"></a>错误代码：4101

- **消息**： `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**：属性 "% propertyName;" 的格式错误或缺少定义。

- **建议**：请检查活动 "% activityName;" 是否已定义正确的数据的属性 "% propertyName;"。


### <a name="error-code--4110"></a>错误代码：4110

- **消息**： `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **原因**： AzureMLExecutePipeline 活动定义不完整。

- **建议**：请检查输入 AZUREMLEXECUTEPIPELINE 活动 JSON 定义是否有链接的服务详细信息。


### <a name="error-code--4111"></a>错误代码：4111

- **消息**： `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**：活动定义不正确。

- **建议**：请检查输入 AZUREMLEXECUTEPIPELINE 活动 JSON 定义是否有正确的链接服务详细信息。


### <a name="error-code--4112"></a>错误代码：4112

- **消息**： `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**：属性 "% propertyName;" 的格式错误或缺少定义。

- **建议**：请检查链接服务是否具有定义了正确数据的属性 "% propertyName;"。


### <a name="error-code--4121"></a>错误代码：4121

- **消息**： `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **原因**：用于访问 Azure ML 服务的凭据已过期。

- **建议**：请验证凭据是否有效，然后重试


### <a name="error-code--4122"></a>错误代码：4122

- **消息**： `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **原因**： Azure ML 服务链接服务中提供的凭据无效或没有操作的权限。

- **建议**：请验证链接服务中的凭据是否有效，以及是否有权访问 Azure ML 服务。


### <a name="error-code--4123"></a>错误代码：4123

- **消息**： `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **原因**： `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **建议**：请检查活动属性的值，以匹配链接服务中指定的已发布 Azure ML 管道的预期负载。


### <a name="error-code--4124"></a>错误代码：4124

- **消息**： `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **原因**：已发布的 Azure ML 管道终结点不存在。

- **建议**：请验证 Azure ml 服务中是否存在链接的服务中指定的已发布 Azure ml 管道终结点。


### <a name="error-code--4125"></a>错误代码：4125

- **消息**： `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **原因**： Azure ML 服务上出现服务器错误。

- **建议**：请稍后重试。 如果问题仍然存在，请联系 Azure ML 服务团队以获得帮助。


### <a name="error-code--4126"></a>错误代码：4126

- **消息**： `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure ML Service for more error logs.`

- **原因**： Azure ML 管道运行失败。

- **建议**：请查看 Azure ML 服务以获得更多错误日志，并修复 ML 管道



## <a name="common"></a>常见

### <a name="error-code--2103"></a>错误代码：2103

- **消息**： `Please provide value for the required property '%propertyName;'.`

- **原因**：未提供属性的值，但在方案中是必需的。

- **建议**：提供消息的值，然后重试。


### <a name="error-code--2104"></a>错误代码：2104

- **消息**： `The type of the property '%propertyName;' is incorrect.`

- **原因**：提供的属性的类型与预期不符。

- **建议**：请修复属性的类型，然后重试。


### <a name="error-code--2105"></a>错误代码：2105

- **消息**： `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **原因**：属性的值无效或不具有预期格式。

- **建议**：请查找属性的文档，并确保提供的值具有预期的格式和类型。


### <a name="error-code--2106"></a>错误代码：2106

- **消息**： `The storage connection string is invalid. %errorMessage;`

- **原因**：存储的连接字符串无效或格式不正确。

- **建议**：请中转到 Azure 门户，找到你的存储，复制连接字符串，然后粘贴到你的链接服务中，然后重试。


### <a name="error-code--2108"></a>错误代码：2108

- **消息**： `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**：请求失败，原因是网络连接性、DNS 失败、服务器证书验证或超时。

- **建议**：使用 Fiddler/Postman 验证请求。


### <a name="error-code--2110"></a>错误代码：2110

- **消息**： `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **原因**：活动中指定的链接服务错误。

- **建议**：请确保链接的服务类型是活动支持的类型之一。 例如，对于 HDI 活动，链接的服务类型可以是 HDInsight 或 HDInsightOnDemand。


### <a name="error-code--2111"></a>错误代码：2111

- **消息**： `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **原因**：提供的属性的类型与预期不符。

- **建议**：请修复属性的类型，然后重试。


### <a name="error-code--2112"></a>错误代码：2112

- **消息**： `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **原因**：云类型不受支持，或者无法确定 EndpointSuffix 中的存储空间。

- **建议**：请使用另一个云中的存储，然后重试。


### <a name="error-code--2128"></a>错误代码：2128

- **消息**： `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：网络连接、DNS 失败、服务器证书验证或超时。

- **建议**：验证你尝试访问的终结点是否响应请求。 你可以使用 Fiddler/Postman 等工具。



## <a name="custom"></a>“自定义”

下表适用于 Azure Batch。
      
### <a name="error-code--2500"></a>错误代码：2500

- **消息**： `Hit unexpected exception and execution failed.`

- **原因**： `Can't launch command, or the program returned an error code.`

- **建议**：确保可执行文件存在。 如果程序已启动，请确保将*stdout*和*stderr*上传到存储帐户。 最好在代码中发出详细日志进行调试。


### <a name="error-code--2501"></a>错误代码：2501

- **消息**： `Cannot access user batch account; please check batch account settings.`

- **原因**：批处理访问密钥或池名称不正确。

- **建议**：在链接的服务中验证池名称和批处理访问密钥。


### <a name="error-code--2502"></a>错误代码：2502

- **消息**： `Cannot access user storage account; please check storage account settings.`

- **原因**：不正确的存储帐户名称或访问密钥。

- **建议**：在链接的服务中验证存储帐户名称和访问密钥。


### <a name="error-code--2504"></a>错误代码：2504

- **消息**： `Operation returned an invalid status code 'BadRequest'.`

- **原因**：自定义活动的 folderPath 中的文件太多。 ResourceFiles 的总大小不能超过32768个字符。

- **建议**：删除不必要的文件。 或对其进行压缩，并添加解压缩命令来提取它们。 例如，使用 `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>错误代码：2505

- **消息**： `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**：自定义活动只支持使用访问密钥的存储帐户。

- **建议**：请参阅错误说明。


### <a name="error-code--2507"></a>错误代码：2507

- **消息**： `The folder path does not exist or is empty: ....`

- **原因**：存储帐户中指定路径处没有文件。

- **建议**：文件夹路径必须包含要运行的可执行文件。


### <a name="error-code--2508"></a>错误代码：2508

- **消息**： `There are duplicate files in the resource folder.`

- **原因**：具有相同名称的多个文件位于 folderPath 的不同子文件夹中。

- **建议**：自定义活动在 folderPath 下平展文件夹结构。  如果需要保留文件夹结构，请压缩文件，并使用解压缩命令将这些文件解压缩到 Azure Batch 中。 例如，使用 `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>错误代码：2509

- **消息**： `Batch   url ... is invalid; it must be in Uri format.`

- **原因**：批处理 url 必须类似于 `https://mybatchaccount.eastus.batch.azure.com`

- **建议**：请参阅错误说明。


### <a name="error-code--2510"></a>错误代码：2510

- **消息**： `An   error occurred while sending the request.`

- **原因**：批处理 URL 无效。

- **建议**：验证批处理 URL。
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>错误代码：200

- **消息**： `Unexpected error happened: '%error;'.`

- **原因**：存在内部服务问题。

- **建议**：若要获得进一步帮助，请联系 ADF 支持。


### <a name="error-code--201"></a>错误代码：201

- **消息**： `JobType %jobType; is not found.`

- **原因**： ADF 不支持新的作业类型。

- **建议**：若要获得进一步帮助，请联系 ADF 支持团队。


### <a name="error-code--202"></a>错误代码：202

- **消息**： `Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **原因**：错误消息应显示出现错误的详细信息。

- **建议**：错误消息应有助于解决问题。 如果没有足够的信息，请联系 ADF 支持获取更多帮助。


### <a name="error-code--203"></a>错误代码：203

- **消息**： `Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **原因**：错误消息应显示出现错误的详细信息。

- **建议**：错误消息应有助于解决问题。 如果没有足够的信息，请联系 ADF 支持获取更多帮助。


### <a name="error-code--204"></a>错误代码：204

- **消息**： `The resumption token is missing for runId '%runId;'.`

- **原因**：存在内部服务问题。

- **建议**：若要获得进一步帮助，请联系 ADF 支持。


### <a name="error-code--205"></a>错误代码：205

- **消息**： `Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **原因**：创建 HDI 点播群集时出错。

- **建议**：若要获得进一步帮助，请联系 ADF 支持。


### <a name="error-code--206"></a>错误代码：206

- **消息**： `The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **原因**：导致此操作的服务出现内部问题。

- **建议**：这可能是暂时性问题。 请重试你的作业，如果问题仍然存在，请与 ADF 支持联系以获得进一步的帮助。


### <a name="error-code--207"></a>错误代码：207

- **消息**： `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **原因**：尝试从主存储帐户确定区域时出现内部错误。

- **建议**：尝试其他存储。 如果这不是可接受的解决方案，请与 ADF 支持团队联系以获得进一步的帮助。


### <a name="error-code--208"></a>错误代码：208

- **消息**： `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **原因**：尝试读取服务主体或实例化 MSI 身份验证时出现内部错误。

- **建议**：请考虑提供有权在所提供的订阅中创建 HDInsight 群集的服务主体，然后重试。 如果这不是可接受的解决方案，请与 ADF 支持团队联系以获得进一步的帮助。


### <a name="error-code--2300"></a>错误代码：2300

- **消息**： `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **原因**：错误消息中包含类似于 "无法解析远程名称" 的消息。这可能意味着提供的群集 URI 无效。


- **建议**：请确保未删除群集，并且提供的 URI 是正确的。 在浏览器中打开 URI 时，应会看到 Ambari UI。 如果群集位于虚拟网络中，则 URI 应为专用 URI。 若要打开它，请使用同一虚拟网络中的 VM。 有关详细信息，请参阅[此](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)内容。
                  

<br>

- **原因**：错误消息中包含类似于 "已取消任务" 的消息，这意味着作业提交超时。

- **建议**：问题可能是常规 HDInsight 连接或网络连接。 首先确认 HDInsight Ambari UI 可从任何浏览器获得。 确认凭据仍然有效。 如果使用自承载集成运行时（IR），请确保从安装自承载 IR 的 VM 或计算机中执行此操作。 然后尝试再次从数据工厂提交作业。 如果仍失败，请与数据工厂团队联系以获取支持。

<br>

- **原因**：当错误消息包含类似于 "用户管理员已被锁定在 Ambari" 或 "未授权： Ambari 用户名或密码不正确" 的消息时，这意味着 HDInsight 的凭据不正确或已过期。

- **建议**：更正凭据并重新部署链接服务。 首先，请确保凭据在 HDInsight 上起作用，方法是在任何浏览器上打开群集 URI 并尝试登录。 如果凭据不起作用，则可以从 Azure 门户重置它们。

<br>

- **原因**：如果错误消息包含类似于 "502-Web 服务器在充当网关或代理服务器时收到了无效响应" 的消息，则 HDInsight 服务将返回此错误。


- **建议**：查看 Azure HDInsight 故障排除文档，例如 https://hdinsight.github.io/ambari/ambari-ui-502-error.html、 https://hdinsight.github.io/spark/spark-thriftserver-errors.htmlhttps://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502。
                  

<br>

- **原因**：当错误消息包含类似于 "无法处理提交作业请求，因为 templeton 服务正忙，因为有太多提交作业请求" 或 "500 joblauncher" 的消息时，无法接受提交应用程序 '，这意味着同时提交到 HDInsight 的作业过多。

- **建议**：考虑限制提交到 HDInsight 的并发作业的数目。 如果作业正在由同一活动提交，则请参阅数据工厂活动并发性。 更改触发器，使并发管道运行随时间而分散。 有关错误的建议，请参阅 HDInsight 文档以调整 templeton。


### <a name="error-code--2301"></a>错误代码：2301

- **消息**： `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **原因**： HDInsight 群集或服务有问题。


- **建议**：当你尝试获取正在运行的作业的状态时，ADF 不会从 HDInsight 群集获得响应，则会发生此错误。 此问题可能是由群集本身的问题引起的，也可能是 HDInsight 服务中断。 请参阅 https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide上的 HDInsight 故障排除文档，或联系支持人员以获得进一步帮助。
                


### <a name="error-code--2302"></a>错误代码：2302

- **消息**： `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **原因**：作业已提交到 HDI 群集，但在此失败。

- **建议**：按照活动运行输出中的 Yarn 日志链接，并在 HDI 输出中查找错误。 如有必要，请与 HDInsight 团队联系以获取支持。


### <a name="error-code--2303"></a>错误代码：2303

- **消息**： `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **原因**：作业已提交到 HDI 群集，但在此失败。

- **建议**：按照活动运行输出中的 Yarn 日志链接，并在 HDI 输出中查找错误。 请重试，或与 HDInsight 团队联系以获取支持（如果需要）。


### <a name="error-code--2304"></a>错误代码：2304

- **消息**： `MSI authentication is not supported on storages for HDI activities.`

- **原因**： HDI 链接服务或 HDI 活动中使用的存储链接的服务已配置为使用不受支持的 MSI 身份验证。

- **建议**：请提供 HDI 链接服务或 HDI 活动中使用的存储帐户的完整连接字符串。


### <a name="error-code--2305"></a>错误代码：2305

- **消息**： `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **原因**： HDI 群集的连接信息不正确，提供的用户无权执行所需的操作，或者 HDInsight 服务在响应来自 ADF 的请求时出现问题。

- **建议**：请确保用户信息正确。 还要验证 HDI 群集的 Ambari UI 是否可以在自承载 ir 的情况下，从安装了 IR 的 VM 的浏览器中打开，也可以在 Azure IR 的情况下从任何计算机打开。


### <a name="error-code--2306"></a>错误代码：2306

- **消息**： `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **原因**：为脚本操作提供的 json 无效。


- **建议**：错误消息应有助于识别问题。 请修复 json 配置，然后重试。 有关详细信息，请查看 https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service。
                


### <a name="error-code--2310"></a>错误代码：2310

- **消息**： `Failed to submit Spark job. Error: '%message;'`

- **原因**： ADF 尝试使用 Livy API （Livy/batch）在 Spark 群集上创建批，但收到了一个错误。

- **建议**：请按照错误消息解决该问题。 如果信息不足以解决问题，请联系 HDI 团队，并向他们提供可在 ADF 监视页面的活动运行输出中找到的批 ID 和作业 ID。


### <a name="error-code--2312"></a>错误代码：2312

- **消息**： `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **原因**：在 HDInsight Spark 群集上作业失败。

- **建议**：请按照 "ADF 监视" 页上的活动 "运行输出" 中的链接，对 HDInsight Spark 群集上的运行进行故障排除。 请联系 HDInsight 支持团队以获得进一步的帮助。


### <a name="error-code--2313"></a>错误代码：2313

- **消息**： `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **原因**：在 HDInsight Spark 群集上删除了该批。

- **建议**：对 HDInsight Spark 群集上的批处理进行故障排除。 若要获得进一步帮助，请联系 HDInsight 支持。 


### <a name="error-code--2328"></a>错误代码：2328

- **消息**： `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**： `The error message should show the details of what went wrong.`

- **建议**：错误消息应有助于解决问题。


### <a name="error-code--2329"></a>错误代码：2329

- **消息**： `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**：错误消息应显示出现错误的详细信息。

- **建议**：错误消息应有助于解决问题。


### <a name="error-code--2331"></a>错误代码：2331

- **消息**： `The file path should not be null or empty.`

- **原因**：提供的文件路径为空。

- **建议**：请为存在的文件提供路径。


### <a name="error-code--2340"></a>错误代码：2340

- **消息**： `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **原因**： HDInsightOnDemand 链接服务不支持通过 SelfHosted IR 执行。

- **建议**：请选择 Azure IR，然后重试。


### <a name="error-code--2341"></a>错误代码：2341

- **消息**： `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **原因**：提供的 URL 的格式不正确。

- **建议**：请修复群集 URL，然后重试。


### <a name="error-code--2342"></a>错误代码：2342

- **消息**： `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **原因**：为群集提供的凭据不正确，或网络配置或连接问题或 IR 连接到群集时出现问题。

- **建议**：  
      1. 通过在浏览器中打开 HDInsight 群集的 Ambari UI，验证凭据是否正确。
      2. 如果群集在 VNet 中并且正在使用自我托管 IR，则的 HDI URL 应为 Vnet 中的专用 URL，这意味着在群集名称后，该 url 应包含 "-int"。 例如，"https://mycluster.azurehdinsight.net/" 应更改为 "https://mycluster-int.azurehdinsight.net/"。
      2. 如果群集在 VNet 中，使用的是自承载 IR 并且使用的是专用 URL，而且连接仍然失败，则安装 IR 的 VM 在连接到 HDI 时出现问题。 连接到安装了 IR 的 VM，并在浏览器中打开 Ambari UI。 使用群集的专用 URL。 此连接应在浏览器中工作。 如果没有，请与 HDInsight 支持团队联系以获得进一步的帮助。
      3. 如果未使用自承载 IR，则应公开访问 HDI 群集。 在浏览器中打开 Ambari UI，并确保它已打开。 如果群集或其上的服务有任何问题，请与 HDInsight 支持团队联系以获取帮助。
      一般来说，在 ADF 链接服务中使用的 HDI 群集 URL 必须可供 ADF IR （自承载或 Azure）访问，以便测试连接通过，并且运行正常。 通过从 VM 或任何公共计算机打开浏览器中的 URL，可以轻松地对其进行验证。
    


### <a name="error-code--2343"></a>错误代码：2343

- **消息**： `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **原因**：用户名或密码为空。

- **建议**：提供正确的凭据以连接到 HDI，然后重试。


### <a name="error-code--2345"></a>错误代码：2345

- **消息**： `Failed to read the content of the hive script. Error: '%message;'`

- **原因**：脚本文件不存在或 ADF 无法连接到脚本的位置。

- **建议**：请验证脚本是否存在，以及关联的链接服务是否具有适当的连接凭据。


### <a name="error-code--2346"></a>错误代码：2346

- **消息**： `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **原因**： ADF 尝试与 HDI 群集建立 ODBC 连接，但失败并出现错误。

- **建议**：错误消息和错误代码应该有助于排查 ODBC 连接错误。 如果无法解决问题，请联系 Azure HDInsight 团队以获得支持。


### <a name="error-code--2347"></a>错误代码：2347

- **消息**： `Hive execution through ODBC failed with error message '%message;'.`

- **原因**： ADF 通过 ODBC 连接将 hive 脚本提交到 HDI 群集，并且该脚本在 HDI 上失败。

- **建议**：在 HDI 群集上执行 hive 脚本失败，并且错误消息和错误代码应有助于进行故障排除。 如果无法解决问题，请联系 Azure HDInsight 团队以获得支持。


### <a name="error-code--2348"></a>错误代码：2348

- **消息**： `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **原因**：未正确设置存储链接服务属性。

- **建议**： HDI 活动的主存储链接服务仅支持完整连接字符串。 请确保未使用 MSI 身份验证或应用程序。


### <a name="error-code--2350"></a>错误代码：2350

- **消息**： `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **原因**：为了连接到文件所在的存储区而提供的凭据不正确，或文件不存在。

- **建议**：当 ADF 为 HDI 活动准备步骤时，会发生此错误。 在将作业提交到 HDI 之前，它会尝试将文件复制到主存储。 请确保文件存在于所提供的位置，存储连接是正确的。 ADF HDI 活动不支持与 HDI 活动相关的存储帐户上的 MSI 身份验证，因此请确保这些链接服务具有完整密钥或正在使用 Azure Key Vault。


### <a name="error-code--2351"></a>错误代码：2351

- **消息**： `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **原因**：文件在指定的路径中不存在。

- **建议**：请检查文件是否确实存在，并且具有指向此文件的连接信息的链接服务是否具有正确的凭据。


### <a name="error-code--2352"></a>错误代码：2352

- **消息**： `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **原因**：未正确设置文件存储链接服务属性。

- **建议**：请确保正确配置了文件存储链接服务的属性。


### <a name="error-code--2353"></a>错误代码：2353

- **消息**： `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **原因**：未正确设置脚本存储链接服务属性。

- **建议**：请确保正确配置了脚本存储链接服务的属性。


### <a name="error-code--2354"></a>错误代码：2354

- **消息**： `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **原因**：活动不支持存储链接服务类型。

- **建议**：请确保所选的链接服务具有活动的支持类型之一。 HDI 活动支持 AzureBlobStorage 和 AzureBlobFSStorage 链接服务。


### <a name="error-code--2355"></a>错误代码：2355

- **消息**： `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **原因**：提供的 commandEnvironment 不正确。

- **建议**：  
      请确保所提供的值类似于： \"commandEnvironment\"： [\"variableName = variableValue\"]，并且每个变量仅出现在列表中。
    


### <a name="error-code--2356"></a>错误代码：2356

- **消息**： `The commandEnvironment already contains a variable named '%variableName;'.`

- **原因**：变量在 commandEnvironment 中提供了两次。

- **建议**：  
      请确保所提供的值类似于： \"commandEnvironment\"： [\"variableName = variableValue\"]，并且每个变量仅出现在列表中。
    


### <a name="error-code--2357"></a>错误代码：2357

- **消息**： `The certificate or password is wrong for ADLS Gen 1 storage.`

- **原因**：提供的凭据不正确。

- **建议**：请验证 ADLS 第1代链接服务中的连接信息，并确保测试连接成功。


### <a name="error-code--2358"></a>错误代码：2358

- **消息**： `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **原因**：为所需属性 "TimeToLive" 提供的值的格式无效。 

- **建议**：请将值更新为建议的范围，然后重试。


### <a name="error-code--2359"></a>错误代码：2359

- **消息**： `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **原因**：为属性 "roles" 提供的值无效。

- **建议**：请将此值更新为建议之一，然后重试。


### <a name="error-code--2360"></a>错误代码：2360

- **消息**： `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **原因**：为 HCatalogLinkedService 提供的连接字符串无效。

- **建议**：请将值更新为正确的 Azure SQL 连接字符串，然后重试。


### <a name="error-code--2361"></a>错误代码：2361

- **消息**： `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **原因**：群集创建失败，并且 ADF 没有从 HDInsight 服务返回错误。

- **建议**：打开 Azure 门户，尝试查找具有所提供名称的 HDI 资源，并检查预配状态。 请联系 HDInsight 支持团队以获得进一步的帮助。


### <a name="error-code--2362"></a>错误代码：2362

- **消息**： `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **原因**：提供的其他存储不是 Azure Blob 存储。

- **建议**：为 HDInsight 按需链接服务提供 Azure Blob 存储帐户作为附加存储。



## <a name="web-activity"></a>Web 活动

### <a name="error-code--2128"></a>错误代码：2128

- **消息**： `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：网络连接、DNS 失败、服务器证书验证或超时。

- **建议**：验证你尝试访问的终结点是否响应请求。 你可以使用 Fiddler/Postman 等工具。


### <a name="error-code--2108"></a>错误代码：2108

- **消息**： `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**：请求失败，原因是网络连接性、DNS 失败、服务器证书验证或超时。

- **建议**：使用 Fiddler/Postman 验证请求。
<br>


#### <a name="more-details"></a>更多详细信息
若要使用 Fiddler 创建监视的 web 应用程序的 HTTP 会话，请执行以下操作：

1. 下载、安装并打开[Fiddler](https://www.telerik.com/download/fiddler)。

1. 如果你的 web 应用程序使用 HTTPS，请跳到 "**工具**" > **Fiddler Options** > **HTTPS**"。 选择 "**捕获 Https 连接**" 和 "**解密 https 通信**"。

![Fiddler 选项](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 如果你的应用程序使用 SSL 证书，请将 Fiddler 证书添加到你的设备。 请参阅 "**工具**" > **Fiddler Options** > **HTTPS** > **操作** > "**将根证书导出到桌面**"。

1. 转到 "**文件** > **捕获流量**"，关闭 "捕获"。 或按**F12**。

1. 清除浏览器的缓存，以便删除所有已缓存的项，并且必须再次下载。

1. 创建请求：

a. 选择 "**编辑器**" 选项卡。

b. 设置 HTTP 方法和 URL。

c. 如果需要，请添加标头和请求正文。

d. 选择”**执行**“。

9. 再次打开流量捕获，并在页面上完成有问题的事务。

10. 请参阅**文件** > **保存** > **所有会话**。

有关详细信息，请参阅[Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)入门。

## <a name="next-steps"></a>后续步骤

有关故障排除的详细信息，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)


            
