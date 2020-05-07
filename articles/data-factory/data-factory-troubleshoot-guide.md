---
title: 排查 Azure 数据工厂问题 | Microsoft Docs
description: 了解如何排查 Azure 数据工厂中的外部控制活动问题。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: f07cc109b21010df89b105576cb9afcf93df774a
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744772"
---
# <a name="troubleshoot-azure-data-factory"></a>排查 Azure 数据工厂问题
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨 Azure 数据工厂中的外部控制活动的常用故障排除方法。

## <a name="connector-and-copy-activity"></a>连接器和复制活动

对于连接器问题（如使用复制活动时遇到错误），请参阅对[Azure 数据工厂连接器进行故障排除](connector-troubleshoot-guide.md)。

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>错误代码：3200

- **消息**：错误403。

- **原因**：`The Databricks access token has expired.`

- **建议**：默认情况下，Azure Databricks 访问令牌的有效期为90天。 创建新的令牌并更新链接的服务。

### <a name="error-code-3201"></a>错误代码：3201

- **消息**：`Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**：`Bad authoring: Notebook path not specified correctly.`

- **建议**：在 Databricks 活动中指定笔记本路径。

<br/> 

- **消息**：`Cluster... does not exist.`

- **原因**：`Authoring error: Databricks cluster does not exist or has been deleted.`

- **建议**：验证 Databricks 群集是否存在。

<br/> 

- **消息**：`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **原因**：`Bad authoring.`

- **建议**：为工作区寻址方案指定绝对路径，或者`dbfs:/folder/subfolder/foo.py`为存储在 DATABRICKS 文件系统（DFS）中的文件指定绝对路径。

<br/> 

- **消息**：`{0} LinkedService should have domain and accessToken as required properties.`

- **原因**：`Bad authoring.`

- **建议**：验证[链接的服务定义](compute-linked-services.md#azure-databricks-linked-service)。

<br/> 

- **消息**：`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**：`Bad authoring.`

- **建议**：验证[链接的服务定义](compute-linked-services.md#azure-databricks-linked-service)。

<br/> 

- **消息**：`Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **原因**：`Bad authoring.`

- **建议**：请参阅错误消息。

<br/>

### <a name="error-code-3202"></a>错误代码：3202

- **消息**：`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **原因**：`Too many Databricks runs in an hour.`

- **建议**：检查使用此 Databricks 工作区的所有管道的作业创建速率。 如果管道启动了太多的 Databricks，则将一些管道迁移到新的工作区。

<br/> 

- **消息**：`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**：`Authoring error: No value provided for the parameter.`

- **建议**：检查管道 JSON 并确保 baseParameters 笔记本中的所有参数都指定一个非空值。

<br/> 

- **消息**： `User: `SimpleUserContext {userId = ...，name =user@company.com，orgId = ...}` is not authorized to access cluster.`

- **原因**：不允许生成访问令牌的用户访问在链接服务中指定的 Databricks 群集。

- **建议**：确保用户在工作区中具有所需的权限。

### <a name="error-code-3203"></a>错误代码：3203

- **消息**：`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**：群集已终止。 对于交互式群集，此问题可能是争用情况。

- **建议**：若要避免此错误，请使用作业群集。

### <a name="error-code-3204"></a>错误代码：3204

- **消息**：`Job execution failed.`

- **原因**：错误消息指示各种问题，例如意外群集状态或特定活动。 通常不会显示任何错误消息。

- **建议**：不适用
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表适用于 U SQL。
 
### <a name="error-code-2709"></a>错误代码：2709

- **消息**：`The access token is from the wrong tenant.`

- **原因**：不正确的 Azure Active Directory （Azure AD）租户。

- **建议**：不正确的 Azure Active Directory （Azure AD）租户。

<br/>

- **消息**：`We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **原因**：此错误是由 Data Lake Analytics 上的限制引起的。

- **建议**：将已提交作业的数量减少到 Data Lake Analytics。 更改活动的数据工厂触发器和并发设置，或增加 Data Lake Analytics 的限制。

<br/> 

- **消息**：`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**：此错误是由 Data Lake Analytics 上的限制引起的。

- **建议**：将已提交作业的数量减少到 Data Lake Analytics。 更改活动的数据工厂触发器和并发设置，或增加 Data Lake Analytics 的限制。

### <a name="error-code-2705"></a>错误代码：2705

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：验证用户为 Data Lake Analytics 作业提供的服务主体或证书是否有权访问 Data Lake Analytics 帐户，以及根文件夹中的默认 Data Lake Storage 实例。

### <a name="error-code-2711"></a>错误代码：2711

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：验证用户为 Data Lake Analytics 作业提供的服务主体或证书是否有权访问 Data Lake Analytics 帐户，以及根文件夹中的默认 Data Lake Storage 实例。

<br/> 

- **消息**：`Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**： U SQL 文件的路径错误，或链接的服务凭据没有访问权限。

- **建议**：验证在链接服务中提供的路径和凭据。

### <a name="error-code-2704"></a>错误代码：2704

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：验证用户为 Data Lake Analytics 作业提供的服务主体或证书是否有权访问 Data Lake Analytics 帐户，以及根文件夹中的默认 Data Lake Storage 实例。

### <a name="error-code-2707"></a>错误代码：2707

- **消息**：`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **原因**：链接的服务中的 Data Lake Analytics 帐户错误。

- **建议**：验证是否提供了正确的帐户。

### <a name="error-code-2703"></a>错误代码：2703

- **消息**：`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **原因**：错误来自 Data Lake Analytics。

- **建议**：作业已提交到 Data Lake Analytics 和脚本，但均失败。 在 Data Lake Analytics 中进行调查。 在门户中，中转到 Data Lake Analytics 帐户，并使用数据工厂活动运行 ID （请勿使用管道运行 ID）查找作业。 作业中提供了有关错误的详细信息，并将帮助你进行故障排除。

   如果解决方法不清楚，请联系 Data Lake Analytics 支持团队并提供作业通用资源定位器（URL），其中包括你的帐户名和作业 ID。
 
## <a name="azure-functions"></a>Azure 函数

### <a name="error-code-3602"></a>错误代码：3602

- **消息**：`Invalid HttpMethod: '%method;'.`

- **原因**： Azure Function 活动不支持在活动有效负载中指定的 Httpmethod。

- **建议**：受支持的 Httpmethods 包括： PUT、POST、GET、DELETE、OPTIONS、HEAD 和 TRACE。

### <a name="error-code-3603"></a>错误代码：3603

- **消息**：`Response Content is not a valid JObject.`

- **原因**：调用的 Azure 函数未在响应中返回 JSON 有效负载。 Azure 数据工厂（ADF） Azure function 活动仅支持 JSON 响应内容。

- **建议**：更新 Azure 函数以返回有效的 JSON 有效负载，如 c # 函数可能返回`(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>错误代码：3606

- **消息**： Azure function 活动缺少功能键。

- **原因**： Azure function 活动定义未完成。

- **建议**：检查输入的 Azure FUNCTION 活动 JSON 定义是否具有名为`functionKey`的属性。

### <a name="error-code-3607"></a>错误代码：3607

- **消息**：`Azure function activity missing function name.`

- **原因**： Azure function 活动定义未完成。

- **建议**：检查输入的 Azure FUNCTION 活动 JSON 定义是否具有名为`functionName`的属性。

### <a name="error-code-3608"></a>错误代码：3608

- **消息**：`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **原因**：活动定义中的 Azure function 详细信息可能不正确。

- **建议**：修复 Azure function 详细信息，然后重试。

### <a name="error-code-3609"></a>错误代码：3609

- **消息**：`Azure function activity missing functionAppUrl.`

- **原因**： Azure function 活动定义未完成。

- **建议**：检查输入的 Azure FUNCTION 活动 JSON 定义是否具有名为`functionAppUrl`的属性。

### <a name="error-code-3610"></a>错误代码：3610

- **消息**：`There was an error while calling endpoint.`

- **原因**：函数 URL 可能不正确。

- **建议**：验证活动 JSON `functionAppUrl`中的值是否正确，然后重试。

### <a name="error-code-3611"></a>错误代码：3611

- **消息**：`Azure function activity missing Method in JSON.`

- **原因**： Azure function 活动定义未完成。

- **建议**：检查输入的 Azure FUNCTION 活动 JSON 定义是否具有名为`method`的属性。

### <a name="error-code-3612"></a>错误代码：3612

- **消息**：`Azure function activity missing LinkedService definition in JSON.`

- **原因**： Azure function 活动定义未完成。

- **建议**：检查输入的 Azure FUNCTION 活动 JSON 定义是否有链接的服务详细信息。

## <a name="azure-machine-learning"></a>Azure 机器学习

### <a name="error-code-4101"></a>错误代码：4101

- **消息**：`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**：属性的格式错误或缺少属性`%propertyName;`的定义。

- **建议**：检查活动`%activityName;`是否具有用正确数据`%propertyName;`定义的属性。

### <a name="error-code-4110"></a>错误代码：4110

- **消息**：`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **原因**： AzureMLExecutePipeline 活动定义未完成。

- **建议**：检查输入 AZUREMLEXECUTEPIPELINE 活动 JSON 定义是否已正确链接服务详细信息。

### <a name="error-code-4111"></a>错误代码：4111

- **消息**：`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**：活动定义不正确。

- **建议**：检查输入 AZUREMLEXECUTEPIPELINE 活动 JSON 定义是否已正确链接服务详细信息。

### <a name="error-code-4112"></a>错误代码：4112

- **消息**：`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**：属性 "% propertyName;" 的格式错误或缺少定义。

- **建议**：检查链接服务是否具有用正确数据`%propertyName;`定义的属性。

### <a name="error-code-4121"></a>错误代码：4121

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：用于访问 Azure 机器学习的凭据已过期。

- **建议**：验证凭据是否有效，然后重试。

### <a name="error-code-4122"></a>错误代码：4122

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**： Azure 机器学习链接服务中提供的凭据无效，或者没有操作的权限。

- **建议**：验证链接服务中的凭据是否有效，以及是否有权访问 Azure 机器学习。

### <a name="error-code-4123"></a>错误代码：4123

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：活动的属性（例如`pipelineParameters` ）对 Azure 机器学习（ML）管道无效。

- **建议**：检查活动属性的值是否与链接服务中指定的已发布 Azure ML 管道的预期有效负载相匹配。

### <a name="error-code-4124"></a>错误代码：4124

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**：已发布的 Azure ML 管道终结点不存在。

- **建议**：验证 Azure 机器学习中是否存在链接服务中指定的已发布 Azure 机器学习管道终结点。

### <a name="error-code-4125"></a>错误代码：4125

- **消息**：`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **原因**： Azure 机器学习上出现服务器错误。

- **建议**：稍后重试。 如果问题仍然存在，请与 Azure 机器学习团队联系以获取帮助。

### <a name="error-code-4126"></a>错误代码：4126

- **消息**：`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **原因**： Azure ML 管道运行失败。

- **建议**：检查 Azure 机器学习是否有更多错误日志，然后修复 ML 管道。

## <a name="common"></a>通用

### <a name="error-code-2103"></a>错误代码：2103

- **消息**：`Please provide value for the required property '%propertyName;'.`

- **原因**：尚未提供属性的所需值。

- **建议**：提供消息的值，然后重试。

### <a name="error-code-2104"></a>错误代码：2104

- **消息**：`The type of the property '%propertyName;' is incorrect.`

- **原因**：提供的属性类型不正确。

- **建议**：修复属性的类型，然后重试。

### <a name="error-code-2105"></a>错误代码：2105

- **消息**：`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **原因**：属性的值无效或未采用预期格式。

- **建议**：参阅属性的文档，并验证提供的值是否包含正确的格式和类型。

### <a name="error-code-2106"></a>错误代码：2106

- **消息**：`The storage connection string is invalid. %errorMessage;`

- **原因**：存储的连接字符串无效或格式不正确。

- **建议**：中转到 Azure 门户并找到你的存储，然后将连接字符串复制并粘贴到你的链接服务中，然后重试。

### <a name="error-code-2108"></a>错误代码：2108

- **消息**：`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**：请求失败，原因是网络连接性、DNS 失败、服务器证书验证或超时。

- **建议**：使用 Fiddler/Postman 验证请求。

### <a name="error-code-2110"></a>错误代码：2110

- **消息**：`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **原因**：活动中指定的链接服务不正确。

- **建议**：验证链接服务类型是否为活动支持的类型之一。 例如，HDI 活动的链接服务类型可以是 HDInsight 或 HDInsightOnDemand。

### <a name="error-code-2111"></a>错误代码：2111

- **消息**：`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **原因**：提供的属性的类型不正确。

- **建议**：修复属性类型，然后重试。

### <a name="error-code-2112"></a>错误代码：2112

- **消息**：`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **原因**：云类型不受支持，或者无法确定 EndpointSuffix 的存储空间。

- **建议**：使用另一个云中的存储，然后重试。

### <a name="error-code-2128"></a>错误代码：2128

- **消息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：网络连接、DNS 失败、服务器证书验证或超时。

- **建议**：验证你尝试访问的终结点是否响应请求。 可以使用 Fiddler/Postman 等工具。

## <a name="custom"></a>自定义

下表适用于 Azure Batch。
 
### <a name="error-code-2500"></a>错误代码：2500

- **消息**：`Hit unexpected exception and execution failed.`

- **原因**：`Can't launch command, or the program returned an error code.`

- **建议**：确保可执行文件存在。 如果程序已启动，请验证*stdout*和*stderr*是否已上传到存储帐户。 最好在代码中包含日志进行调试。

### <a name="error-code-2501"></a>错误代码：2501

- **消息**：`Cannot access user batch account; please check batch account settings.`

- **原因**：批处理访问密钥或池名称不正确。

- **建议**：在链接的服务中验证池名称和批处理访问密钥。

### <a name="error-code-2502"></a>错误代码：2502

- **消息**：`Cannot access user storage account; please check storage account settings.`

- **原因**：不正确的存储帐户名称或访问密钥。

- **建议**：在链接的服务中验证存储帐户名称和访问密钥。

### <a name="error-code-2504"></a>错误代码：2504

- **消息**：`Operation returned an invalid status code 'BadRequest'.`

- **原因**：自定义活动`folderPath`的中的文件太多。 的总大小`resourceFiles`不能超过32768个字符。

- **建议**：删除不必要的文件，或者对其进行压缩，并添加解压缩命令来提取它们。
   
   例如，使用 `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>错误代码：2505

- **消息**：`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**：自定义活动只支持使用访问密钥的存储帐户。

- **建议**：请参阅错误说明。

### <a name="error-code-2507"></a>错误代码：2507

- **消息**：`The folder path does not exist or is empty: ...`

- **原因**：存储帐户中指定路径处没有文件。

- **建议**：文件夹路径必须包含要运行的可执行文件。

### <a name="error-code-2508"></a>错误代码：2508

- **消息**：`There are duplicate files in the resource folder.`

- **原因**：具有相同名称的多个文件位于 folderPath 的不同子文件夹中。

- **建议**：自定义活动在 folderPath 下平展文件夹结构。 如果需要保留文件夹结构，请压缩文件，并使用一个 unzip 命令将其解压缩到 Azure Batch 中。
   
   例如，使用 `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>错误代码：2509

- **消息**：`Batch url ... is invalid; it must be in Uri format.`

- **原因**：批处理 url 必须与`https://mybatchaccount.eastus.batch.azure.com`

- **建议**：请参阅错误说明。

### <a name="error-code-2510"></a>错误代码：2510

- **消息**：`An error occurred while sending the request.`

- **原因**：批处理 URL 无效。

- **建议**：验证批处理 URL。
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-200"></a>错误代码：200

- **消息**：`Unexpected error happened: '%error;'.`

- **原因**：存在内部服务问题。

- **建议**：联系 ADF 支持以获得进一步的帮助。

### <a name="error-code-201"></a>错误代码：201

- **消息**：`JobType %jobType; is not found.`

- **原因**： ADF 不支持新的作业类型。

- **建议**：联系 ADF 支持团队以获得进一步的帮助。

### <a name="error-code-202"></a>错误代码：202

- **消息**：`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **原因**：错误消息中包含错误的详细信息。

- **建议**：错误消息的详细信息可帮助你解决问题。 如果没有足够的信息，请与 ADF 支持联系以获得进一步的帮助。

### <a name="error-code-203"></a>错误代码：203

- **消息**：`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **原因**：错误消息中包含错误的详细信息。

- **建议**：错误消息的详细信息可帮助你解决问题。 如果没有足够的信息，请与 ADF 支持联系以获得进一步的帮助。

### <a name="error-code-204"></a>错误代码：204

- **消息**：`The resumption token is missing for runId '%runId;'.`

- **原因**：存在内部服务问题。

- **建议**：联系 ADF 支持以获得进一步的帮助。

### <a name="error-code-205"></a>错误代码：205

- **消息**：`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **原因**：创建 HDI 点播群集时出错。

- **建议**：联系 ADF 支持以获得进一步的帮助。

### <a name="error-code-206"></a>错误代码：206

- **消息**：`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **原因**：导致此错误的服务出现内部问题。

- **建议**：此问题可能是暂时性的。 重试作业，如果问题仍然存在，请与 ADF 支持联系以获得进一步的帮助。

### <a name="error-code-207"></a>错误代码：207

- **消息**：`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **原因**：尝试从主存储帐户确定区域时出现内部错误。

- **建议**：尝试其他存储。 如果此选项不是可接受的解决方案，请与 ADF 支持团队联系以获取进一步的帮助。

### <a name="error-code-208"></a>错误代码：208

- **消息**：`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **原因**：尝试读取服务主体或实例化 MSI 身份验证时出现内部错误。

- **建议**：考虑提供一个服务主体，该服务主体有权在所提供的订阅中创建 HDInsight 群集，然后重试。 验证是否[正确设置了管理标识](https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities)。

   如果此选项不是可接受的解决方案，请与 ADF 支持团队联系以获取进一步的帮助。

### <a name="error-code-2300"></a>错误代码：2300

- **消息**：`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **原因**：错误消息中包含类似于`The remote name could not be resolved.`的消息。 提供的群集 URI 可能无效。

- **建议**：验证是否未删除群集，并且提供的 URI 是否正确。 在浏览器中打开该 URI 时，应会看到 Ambari UI。 如果该群集位于虚拟网络中，则 URI 应是专用 URI。 若要打开它，请使用属于同一虚拟网络的虚拟机（VM）。

   有关详细信息，请参阅[直接连接到 Apache Hadoop 服务](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)。
 
 </br>

- **原因**：如果错误消息包含类似于`A task was canceled.`的消息，则作业提交超时。

- **建议**：问题可能是常规 HDInsight 连接或网络连接。 首先确认是否可以从任何浏览器打开 HDInsight Ambari UI。 然后检查凭据是否仍然有效。
   
   如果使用的是自承载集成运行时（IR），请从安装自承载 IR 的 VM 或计算机执行此步骤。 然后再次尝试从数据工厂提交作业。 如果仍然失败，请联系数据工厂团队获得支持。

   有关详细信息，请参阅[Ambari WEB UI](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-manage-ambari#ambari-web-ui)。

 </br>

- **原因**：当错误消息包含类似于`User admin is locked out in Ambari`或`Unauthorized: Ambari user name or password is incorrect`的消息时，HDInsight 的凭据不正确或已过期。

- **建议**：更正凭据并重新部署链接服务。 首先，通过在任何浏览器上打开群集 URI 并尝试登录来验证凭据在 HDInsight 上是否有效。 如果凭据无效，可从 Azure 门户重置凭据。

   对于 ESP 群集，通过[自助服务密码重置](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password)重置密码。

 </br>

- **原因**：如果错误消息中包含类似于`502 - Web server received an invalid response while acting as a gateway or proxy server`的消息，则 HDInsight 服务将返回此错误。

- **建议**：在关闭 Ambari 服务器进程时，通常会出现502错误。 可以通过重新启动头节点来重新启动 Ambari 服务。

    1. 使用 SSH 连接到 HDInsight 上的某个节点。
    1. 通过运行`ping headnodehost`标识活动头节点主机。
    1. 使用 SSH 连接到活动头节点上的 Ambari 服务器。 
    1. 重新启动活动头节点。

       有关详细信息，请参阅 Azure HDInsight 故障排除文档。 例如：

       * [Ambari UI 502 错误](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [用于 Apache Spark thrift 服务器的 RpcTimeoutException](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception)
       * [排查应用程序网关中的网关错误错误](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)。

 </br>

- **原因**：如果错误消息包含类似于`Unable to service the submit job request as templeton service is busy with too many submit job requests`或`Queue root.joblauncher already has 500 applications, cannot accept submission of application`的消息，则会同时向 HDInsight 提交太多作业。

- **建议**：限制提交到 HDInsight 的并发作业的数目。 如果这些作业是同一活动提交的，请参阅“数据工厂活动并发性”。 更改触发器，将并发管道运行分散到不同的时间。

   如错误的建议，请`templeton.parallellism.job.submit`参阅[HDInsight 文档](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors)进行调整。

### <a name="error-code-2301"></a>错误代码：2301

- **消息**：`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **原因**： HDInsight 群集或服务有问题。

- **建议**：当 ADF 尝试请求正在运行的作业的状态时，如果 ADF 未收到 HDInsight 群集的响应，则会出现此错误。 此问题可能出现在群集本身，或者 HDInsight 服务可能有中断。

   请参阅https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide中的 HDInsight 疑难解答文档，或者联系其支持部门以获得进一步帮助。

### <a name="error-code-2302"></a>错误代码：2302

- **消息**：`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **原因**：作业已提交到 HDI 群集，但在此失败。

- **建议**： 

 1. 检查 Ambari UI：
    1. 确保所有服务仍在运行。
    1. 在 Ambari UI 中，检查仪表板中的 "警报" 部分。
       1. 有关警报和警报的解决方法的详细信息，请参阅[管理和监视群集](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)。
    1. 查看 YARN 内存。 如果你的 YARN 内存很高，则可能会延迟处理你的作业。 如果没有足够的资源来容纳 Spark 应用程序/作业，请扩展群集以确保群集具有足够的内存和内核。 
 1. 运行示例测试作业。
    1. 如果在 HDInsight 后端上运行相同的作业，请检查它是否成功。 有关示例运行的示例，请参阅[运行 HDInsight 中包含的 MapReduce 示例](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-run-samples-linux) 
 1. 如果作业仍在 HDInsight 上失败，请查看要提供给支持的应用程序日志和信息：
    1. 检查作业是否已提交到 YARN。 如果作业未提交到 yarn，请使用`--master yarn`。
    1. 如果应用程序已完成执行，则收集 YARN 应用程序的开始时间和结束时间。 如果应用程序未完成执行，请收集开始时间/启动时间。
    1. 检查并收集应用程序日志`yarn logs -applicationId <Insert_Your_Application_ID>`。
    1. 检查并收集`/var/log/hadoop-yarn/yarn`目录下的 yarn 资源管理器日志。
    1. 如果这些步骤不足以解决问题，请联系 Azure HDInsight 团队以获取支持，并提供上述日志和时间戳。

### <a name="error-code-2303"></a>错误代码：2303

- **消息**：`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **原因**：作业已提交到 HDI 群集，但在此失败。

- **建议**： 

 1. 检查 Ambari UI：
    1. 确保所有服务仍在运行。
    1. 在 Ambari UI 中，检查仪表板中的 "警报" 部分。
       1. 有关警报和警报的解决方法的详细信息，请参阅[管理和监视群集](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)。
    1. 查看 YARN 内存。 如果你的 YARN 内存很高，则可能会延迟处理你的作业。 如果没有足够的资源来容纳 Spark 应用程序/作业，请扩展群集以确保群集具有足够的内存和内核。 
 1. 运行示例测试作业。
    1. 如果在 HDInsight 后端上运行相同的作业，请检查它是否成功。 有关示例运行的示例，请参阅[运行 HDInsight 中包含的 MapReduce 示例](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-run-samples-linux) 
 1. 如果作业仍在 HDInsight 上失败，请查看要提供给支持的应用程序日志和信息：
    1. 检查作业是否已提交到 YARN。 如果作业未提交到 yarn，请使用`--master yarn`。
    1. 如果应用程序已完成执行，则收集 YARN 应用程序的开始时间和结束时间。 如果应用程序未完成执行，请收集开始时间/启动时间。
    1. 检查并收集应用程序日志`yarn logs -applicationId <Insert_Your_Application_ID>`。
    1. 检查并收集`/var/log/hadoop-yarn/yarn`目录下的 yarn 资源管理器日志。
    1. 如果这些步骤不足以解决问题，请联系 Azure HDInsight 团队以获取支持，并提供上述日志和时间戳。

### <a name="error-code-2304"></a>错误代码：2304

- **消息**：`MSI authentication is not supported on storages for HDI activities.`

- **原因**：使用不受支持的 MSI 身份验证配置 HDINSIGHT （HDI）链接服务或 HDI 活动中使用的存储链接服务。

- **建议**：提供 HDI 链接服务或 HDI 活动中使用的存储帐户的完整连接字符串。

### <a name="error-code-2305"></a>错误代码：2305

- **消息**：`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **原因**： HDI 群集的连接信息不正确，提供的用户无权执行所需的操作，或者 HDInsight 服务在响应来自 ADF 的请求时出现问题。

- **建议**：验证用户信息是否正确，以及是否可以从安装了 IR 的 VM （适用于自承载 ir）的浏览器中打开 HDI 群集的 Ambari UI，或从任何计算机（对于 Azure IR）打开。

### <a name="error-code-2306"></a>错误代码：2306

- **消息**：`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **原因**：为脚本操作提供的 JSON 无效。

- **建议**：错误消息应有助于识别问题。 请修复 json 配置并重试。

   有关详细信息，请查看[Azure HDInsight 按需链接服务](https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service)。

### <a name="error-code-2310"></a>错误代码：2310

- **消息**：`Failed to submit Spark job. Error: '%message;'`

- **原因**： ADF 尝试使用 Livy API （Livy/batch）在 Spark 群集上创建批，但收到了一个错误。

- **建议**：按照错误消息解决该问题。 如果信息不足以解决问题，请联系 HDI 团队，并向他们提供批 ID 和作业 ID，该 id 可在 ADF 监视页的活动运行输出中找到。 若要进一步进行故障排除，请收集批处理作业的完整日志。

   有关如何收集完整日志的详细信息，请参阅[获取批处理作业的完整日志](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)。

### <a name="error-code-2312"></a>错误代码：2312

- **消息**：`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **原因**：在 HDInsight Spark 群集上作业失败。

- **建议**：按照 "ADF 监视" 页中的活动运行输出中的链接，对 HDInsight Spark 群集上的运行进行故障排除。 联系 HDInsight 支持团队以获得更多帮助。

   有关如何收集完整日志的详细信息，请参阅[获取批处理作业的完整日志](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)。

### <a name="error-code-2313"></a>错误代码：2313

- **消息**：`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **原因**：在 HDInsight Spark 群集上删除了该批。

- **建议**：对 HDInsight Spark 群集上的批处理进行故障排除。 联系 HDInsight 支持部门以获得更多帮助。 

   有关如何收集完整日志的详细信息，请参阅[获取批处理作业的完整日志](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)，并共享具有 HDInsight 支持的完整日志以获得进一步帮助。

### <a name="error-code-2328"></a>错误代码：2328

- **消息**：`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**：错误消息应显示出现错误的详细信息。

- **建议**：错误消息应有助于解决问题。

### <a name="error-code-2329"></a>错误代码：2329

- **消息**：`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **原因**：错误消息应显示出现错误的详细信息。

- **建议**：错误消息应有助于解决问题。

### <a name="error-code-2331"></a>错误代码：2331

- **消息**：`The file path should not be null or empty.`

- **原因**：提供的文件路径为空。

- **建议**：提供存在的文件的路径。

### <a name="error-code-2340"></a>错误代码：2340

- **消息**：`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **原因**： HDInsightOnDemand 链接服务不支持通过 SelfHosted IR 执行。

- **建议**：选择 Azure IR，然后重试。

### <a name="error-code-2341"></a>错误代码：2341

- **消息**：`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **原因**：提供的 URL 的格式不正确。

- **建议**：修复群集 URL，然后重试。

### <a name="error-code-2342"></a>错误代码：2342

- **消息**：`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **原因**：为群集提供的凭据不正确，或网络配置或连接有问题，或者 IR 在连接到群集时出现问题。

- **建议**： 
    1. 通过在浏览器中打开 HDInsight 群集的 Ambari UI，验证凭据是否正确。
    1. 如果群集位于虚拟网络（VNet）中并且正在使用自我托管 IR，则 HDI URL 必须是 Vnet 中的专用 URL，并且应在群集名称后列出 "-int"。
    
       例如，将 `https://mycluster.azurehdinsight.net/` 更改为 `https://mycluster-int.azurehdinsight.net/`。 请注意`-int`后`mycluster`，但在`.azurehdinsight.net`
    1. 如果群集在 VNet 中，将使用自承载 IR，并使用专用 URL，但连接仍然失败，则安装 IR 的 VM 在连接到 HDI 时出现问题。 
    
       连接到安装了 IR 的 VM，并在浏览器中打开 Ambari UI。 对群集使用专用 URL。 应该能够在浏览器中建立此连接。 如果不能，请联系 HDInsight 支持团队以获得更多帮助。
    1. 如果未使用自承载 IR，则应公开访问 HDI 群集。 在浏览器中打开 Ambari UI，并检查它是否已打开。 如果群集或其上的服务出现任何问题，请联系 HDInsight 支持团队获得帮助。

       Adf 链接服务中使用的 HDI 群集 URL 必须可用于 ADF IR （自承载或 Azure），才能使测试连接通过，并使运行生效。 可以通过从 VM 或任何公共计算机打开浏览器中的 URL 来验证此状态。

### <a name="error-code-2343"></a>错误代码：2343

- **消息**：`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **原因**：用户名或密码为空。

- **建议**：提供正确的凭据以连接到 HDI，然后重试。

### <a name="error-code-2345"></a>错误代码：2345

- **消息**：`Failed to read the content of the hive script. Error: '%message;'`

- **原因**：脚本文件不存在或 ADF 无法连接到脚本的位置。

- **建议**：验证脚本是否存在，以及关联的链接服务是否具有连接的正确凭据。

### <a name="error-code-2346"></a>错误代码：2346

- **消息**：`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **原因**： ADF 尝试与 HDI 群集建立开放式数据库连接（ODBC）连接，但失败并出现错误。

- **建议**： 

   1. 确认正确设置了 ODBC/Java Database Connectivity （JDBC）连接。
      1. 对于 JDBC，如果你使用的是同一虚拟网络，则可以从以下链接获取此连接：<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. 若要确保正确设置了 JDBC，请参阅[在 HDInsight 中通过 JDBC 驱动程序查询 Apache Hive](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver)。
      1. 对于开放式数据库（ODB），请参阅[教程：使用 ODBC 和 PowerShell 查询 Apache Hive](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell) ，以确保具有正确的设置。 
   1. 验证 Hiveserver2、Hive 元存储和 Hiveserver2 Interactive 是否处于活动状态且正常工作。 
   1. 检查 Ambari 用户界面（UI）：
      1. 确保所有服务仍在运行。
      1. 在 Ambari UI 中，检查仪表板中的 "警报" 部分。
         1. 有关警报和警报的解决方法的详细信息，请参阅[管理和监视群集](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)。
   1. 如果这些步骤不足以解决问题，请联系 Azure HDInsight 团队。

### <a name="error-code-2347"></a>错误代码：2347

- **消息**：`Hive execution through ODBC failed with error message '%message;'.`

- **原因**： ADF 通过 ODBC 连接将 hive 脚本提交到 HDI 群集，并且该脚本在 HDI 上失败。

- **建议**： 

   1. 确认正确设置了 ODBC/Java Database Connectivity （JDBC）连接。
      1. 对于 JDBC，如果你使用的是同一虚拟网络，则可以从以下链接获取此连接：<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. 若要确保正确设置了 JDBC，请参阅[在 HDInsight 中通过 JDBC 驱动程序查询 Apache Hive](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver)。
      1. 对于开放式数据库（ODB），请参阅[教程：使用 ODBC 和 PowerShell 查询 Apache Hive](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell) ，以确保具有正确的设置。 
   1. 验证 Hiveserver2、Hive 元存储和 Hiveserver2 Interactive 是否处于活动状态且正常工作。 
   1. 检查 Ambari 用户界面（UI）：
      1. 确保所有服务仍在运行。
      1. 在 Ambari UI 中，检查仪表板中的 "警报" 部分。
         1. 有关警报和警报的解决方法的详细信息，请参阅[管理和监视群集](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)。
   1. 如果这些步骤不足以解决问题，请联系 Azure HDInsight 团队。

### <a name="error-code-2348"></a>错误代码：2348

- **消息**：`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **原因**：未正确设置存储链接服务属性。

- **建议**： HDI 活动的主存储链接服务仅支持完整连接字符串。 验证你未使用 MSI 授权或应用程序。

### <a name="error-code-2350"></a>错误代码：2350

- **消息**：`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **原因**：为了连接到文件所在的存储区而提供的凭据不正确，或文件不存在。

- **建议**：如果 ADF 准备 HDI 活动，并在将作业提交到 HDI 之前尝试将文件复制到主存储，则会出现此错误。 请检查文件是否存在于提供的位置，以及存储连接是否正确。 由于 ADF HDI 活动不支持 HDI 活动相关的存储帐户上的 MSI 身份验证，请验证这些链接服务是否具有完整密钥或正在使用 Azure Key Vault。

### <a name="error-code-2351"></a>错误代码：2351

- **消息**：`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **原因**：文件在指定的路径中不存在。

- **建议**：检查文件是否确实存在，并且具有指向此文件的连接信息的链接服务是否具有正确的凭据。

### <a name="error-code-2352"></a>错误代码：2352

- **消息**：`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **原因**：未正确设置文件存储链接服务属性。

- **建议**：验证是否正确配置了文件存储链接服务的属性。

### <a name="error-code-2353"></a>错误代码：2353

- **消息**：`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **原因**：未正确设置脚本存储链接服务属性。

- **建议**：验证是否正确配置了脚本存储链接服务的属性。

### <a name="error-code-2354"></a>错误代码：2354

- **消息**：`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **原因**：活动不支持存储链接服务类型。

- **建议**：验证所选链接服务是否具有活动的支持类型之一。 HDI 活动支持 AzureBlobStorage 和 AzureBlobFSStorage 链接服务。

   有关详细信息，请参阅[比较用于 Azure HDInsight 群集的存储选项](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options)

### <a name="error-code-2355"></a>错误代码：2355

- **消息**：`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **原因**：为提供的值`commandEnvironment`不正确。

- **建议**：验证提供的值是否类似于：
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    还要验证每个变量是否只出现在列表中。

### <a name="error-code-2356"></a>错误代码：2356

- **消息**：`The commandEnvironment already contains a variable named '%variableName;'.`

- **原因**：为提供的值`commandEnvironment`不正确。

- **建议**：验证提供的值是否类似于：
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    还要验证每个变量是否只出现在列表中。

### <a name="error-code-2357"></a>错误代码：2357

- **消息**：`The certificate or password is wrong for ADLS Gen 1 storage.`

- **原因**：提供的凭据不正确。

- **建议**：验证 ADLS 第1代中的连接信息是否链接到服务，并验证测试连接是否成功。

### <a name="error-code-2358"></a>错误代码：2358

- **消息**：`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **原因**：为所需属性`TimeToLive`提供的值的格式无效。 

- **建议**：将值更新为建议的范围，然后重试。

### <a name="error-code-2359"></a>错误代码：2359

- **消息**：`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **原因**：为属性`roles`提供的值无效。

- **建议**：将值更新为建议之一，然后重试。

### <a name="error-code-2360"></a>错误代码：2360

- **消息**：`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **原因**：为提供的连接字符串`HCatalogLinkedService`无效。

- **建议**：将值更新为正确的 Azure SQL 连接字符串，然后重试。

### <a name="error-code-2361"></a>错误代码：2361

- **消息**：`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **原因**：群集创建失败，并且 ADF 没有从 HDInsight 服务返回错误。

- **建议**：打开 Azure 门户并尝试查找具有所提供名称的 HDI 资源，然后检查预配状态。 联系 HDInsight 支持团队以获得更多帮助。

### <a name="error-code-2362"></a>错误代码：2362

- **消息**：`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **原因**：提供的其他存储不是 Azure Blob 存储。

- **建议**：提供 Azure Blob 存储帐户作为 HDInsight 按需链接服务的附加存储。

## <a name="web-activity"></a>Web 活动

### <a name="error-code-2128"></a>错误代码：2128

- **消息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：此问题的原因是网络连接、DNS 失败、服务器证书验证或超时。

- **建议**：验证你尝试访问的终结点是否响应请求。 你可以使用**Fiddler/Postman**等工具。

### <a name="error-code-2108"></a>错误代码：2108

- **消息**：`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **原因**：请求失败，原因是网络连接性、DNS 失败、服务器证书验证或超时。

- **建议**：使用 Fiddler/Postman 验证请求。

#### <a name="more-details"></a>更多详细信息
若要使用**Fiddler**创建监视的 web 应用程序的 HTTP 会话，请执行以下操作：

1. 下载、安装并打开 [Fiddler](https://www.telerik.com/download/fiddler)。

1. 如果你的 web 应用程序使用 https，请跳到**Tools** > **Fiddler Options** > **https**。

   1. 在 "HTTPS" 选项卡中，选择 "**捕获 Https 连接**" 和 "**解密 https 通信**"。

      ![Fiddler 选项](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 如果你的应用程序使用 TLS/SSL 证书，请将 Fiddler 证书添加到你的设备。

   中转到： **Tools** > **Fiddler Options** > **HTTPS** > **操作** > **将根证书导出到桌面**。

1. 转到**文件** > **捕获流量**关闭捕获。 或者按 **F12**。

1. 清除浏览器缓存以删除所有已缓存的项；必须重新下载这些项。

1. 创建请求：

1. 选择“编辑器”选项卡。****

   1. 设置 HTTP 方法和 URL。
 
   1. 如果需要，请添加标头和请求正文。

   1. 选择 "**执行**"。

1. 再次打开流量捕获，并在页面上完成相关的事务。

1. 请**参阅：** > **保存** > **所有会话**。

有关详细信息，请参阅 [Fiddler入门](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

* [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
* [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
* [Azure 视频](https://azure.microsoft.com/resources/videos/index/)
* [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home)
