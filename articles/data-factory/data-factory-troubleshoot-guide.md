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
ms.openlocfilehash: ed466b072a771c3aa288a96fa4a0037c31b875f9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091997"
---
# <a name="troubleshoot-azure-data-factory"></a>排查 Azure 数据工厂问题

本文探讨了 Azure 数据工厂中的外部控制活动的常见故障排除方法。

## <a name="connector-and-copy-activity"></a>连接器和复制活动

有关连接器问题（例如，使用复制活动时遇到错误），请参阅对[Azure 数据工厂连接器进行故障排除](connector-troubleshoot-guide.md)。

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>错误代码：3200

- **消息**：错误403。

- **原因**：`The Databricks access token has expired.`

- **建议**：默认情况下，Azure Databricks 访问令牌的有效期为90天。 创建新的令牌并更新链接的服务。


### <a name="error-code--3201"></a>错误代码：3201

- **消息**：`Missing required field: settings.task.notebook_task.notebook_path.`

- **原因**：`Bad authoring: Notebook path not specified correctly.`

- **建议**：在 Databricks 活动中指定笔记本路径。

<br/>

- **消息**：`Cluster   ... does not exist.`

- **原因**：`Authoring error: Databricks cluster does not exist or has been deleted.`

- **建议**：验证 Databricks 群集是否存在。

<br/>

- **消息**：`Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **原因**：`Bad authoring.`

- **建议**：请为工作区寻址方案指定绝对路径，或者`dbfs:/folder/subfolder/foo.py`为存储在 Databricks 文件系统中的文件指定绝对路径。

<br/>

- **消息**：`{0} LinkedService should have domain and accessToken as required properties.`

- **原因**：`Bad authoring.`

- **建议**：验证[链接的服务定义](compute-linked-services.md#azure-databricks-linked-service)。

<br/>

- **消息**：`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **原因**：`Bad authoring.`

- **建议**：验证[链接的服务定义](compute-linked-services.md#azure-databricks-linked-service)。

<br/>

- **消息**：`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **原因**：`Bad authoring.`

- **建议**：请参阅错误消息。 

<br/>

### <a name="error-code--3202"></a>错误代码：3202

- **消息**：`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **原因**：`Too many Databricks runs in an hour.`

- **建议**：检查使用此 Databricks 工作区的所有管道的作业创建速率。  如果管道启动了太多的 Databricks, 则将一些管道迁移到新的工作区。

<br/>

- **消息**：`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **原因**：`Authoring error: No value provided for the parameter.`

- **建议**：检查管道 JSON 并确保 baseParameters 笔记本中的所有参数都指定一个非空值。

<br/>

- **消息**：`User: `SimpleUserContext {userId = ...，name =user@company.com，orgId = ...}` is not   authorized to access cluster.`

- **原因**：不允许生成访问令牌的用户访问在链接服务中指定的 Databricks 群集。

- **建议**：确保用户在工作区中具有所需的权限。


### <a name="error-code--3203"></a>错误代码：3203

- **消息**：`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **原因**：群集已终止。 对于交互式群集, 这可能是一个争用情况。

- **建议**：避免此错误的最佳方式是使用作业群集。


### <a name="error-code--3204"></a>错误代码：3204

- **消息**：`Job execution failed.`

- **原因**：错误消息指示各种问题, 例如意外群集状态或特定活动。 通常不会显示任何错误消息。 

- **建议**：不可用


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表适用于 U SQL。


### <a name="error-code--2709"></a>错误代码：2709

- **消息**：`The access token is from the wrong tenant.`

- **原因**：Azure Active Directory (Azure AD) 租户不正确。

- **建议**：Azure Active Directory (Azure AD) 租户不正确。

<br/>

- **消息**：`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **原因**：此错误是由 Data Lake Analytics 上的限制引起的。

- **建议**：通过更改活动的数据工厂触发器和并发设置, 将提交的作业数减少到 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。

<br/>

- **消息**：`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **原因**：此错误是由 Data Lake Analytics 上的限制引起的。 

- **建议**：通过更改活动的数据工厂触发器和并发设置, 将提交的作业数减少到 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。


### <a name="error-code--2705"></a>错误代码：2705

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：确保用户为 Data Lake Analytics 作业提供的服务主体或证书可以访问根文件夹中的 Data Lake Analytics 帐户和默认 Data Lake Storage 实例。


### <a name="error-code--2711"></a>错误代码：2711

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：确保用户为 Data Lake Analytics 作业提供的服务主体或证书可以访问根文件夹中的 Data Lake Analytics 帐户和默认 Data Lake Storage 实例。

<br/>

- **消息**：`Cannot find the 'Azure Data Lake Store' file or folder.`

- **原因**：到 U SQL 文件的路径错误, 或链接的服务凭据没有访问权限。

- **建议**：验证在链接服务中提供的路径和凭据。


### <a name="error-code--2704"></a>错误代码：2704

- **消息**：`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **原因**：服务主体或证书无权访问存储中的文件。

- **建议**：确保用户为 Data Lake Analytics 作业提供的服务主体或证书可以访问根文件夹中的 Data Lake Analytics 帐户和默认 Data Lake Storage 实例。


### <a name="error-code--2707"></a>错误代码：2707

- **消息**：`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **原因**：链接的服务中的 Data Lake Analytics 帐户错误。

- **建议**：验证是否提供了正确的帐户。


### <a name="error-code--2703"></a>错误代码：2703

- **消息**：`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **原因**：错误来自 Data Lake Analytics。 

- **建议**：如示例中所示的错误表示作业已提交到 Data Lake Analytics, 但脚本失败。 在 Data Lake Analytics 中进行调查。 在门户中, 中转到 Data Lake Analytics 帐户, 并使用数据工厂活动运行 ID (不是管道运行 ID) 查找作业。 其中提供了有关错误的详细信息, 并将帮助你进行故障排除。 如果解决方法不清楚, 请联系 Data Lake Analytics 支持团队并提供作业 URL, 其中包括你的帐户名称和作业 ID。



## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>错误代码：3602

- **消息**：`Invalid HttpMethod: {method}.`

- **原因**：Azure Function 活动不支持在活动有效负载中指定的 Http 方法。 

- **建议**：支持的 Http 方法有 PUT、POST、GET、DELETE、OPTIONS、HEAD 和 TRACE。


### <a name="error-code--3603"></a>错误代码：3603

- **消息**：`Response content is not a valid JObject.`

- **原因**：调用的 Azure 函数未在响应中返回 JSON 有效负载。 数据工厂中的 Azure function 活动仅支持 JSON 响应内容。 

- **建议**：更新 Azure 函数以返回有效的 JSON 有效负载。 例如, C#函数可返回`(ActionResult)new<OkObjectResult("{` \"Id\":\"123。\"`}");`


### <a name="error-code--3606"></a>错误代码：3606

- **消息**：`Azure function activity missing function key.`

- **原因**：Azure function 活动定义不完整。 

- **建议**：请检查输入 AzureFunction 活动 JSON 定义是否包含名为 "functionKey" 的属性。


### <a name="error-code--3607"></a>错误代码：3607

- **消息**：`Azure function activity missing function name.`

- **原因**：Azure function 活动定义不完整。 

- **建议**：请检查输入 AzureFunction 活动 JSON 定义是否包含名为 "functionName" 的属性。


### <a name="error-code--3608"></a>错误代码：3608

- **消息**：`Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **原因**：活动定义中的 Azure function 详细信息可能不正确。 

- **建议**：请修复 azure function 详细信息，然后重试。


### <a name="error-code--3609"></a>错误代码：3609

- **消息**：`Azure function activity missing functionAppUrl.` 

- **原因**：Azure function 活动定义不完整。 

- **建议**：请检查输入 AzureFunction 活动 JSON 定义是否包含名为 "functionAppUrl" 的属性。


### <a name="error-code--3610"></a>错误代码：3610

- **消息**：`There was an error while calling endpoint.`

- **原因**：函数 URL 可能不正确。

- **建议**：请确保活动 JSON 中 "functionAppUrl" 的值正确，然后重试。


### <a name="error-code--3611"></a>错误代码：3611

- **消息**：`Azure function activity missing Method in JSON.` 

- **原因**：Azure function 活动定义不完整。

- **建议**：检查输入 AzureFunction 活动 JSON 定义是否具有名为 "method" 的属性。


### <a name="error-code--3612"></a>错误代码：3612

- **消息**：`Azure function activity missing LinkedService definition in JSON.`

- **原因**：Azure function 活动定义可能不完整。

- **建议**：请检查输入 AzureFunction 活动 JSON 定义是否有链接的服务详细信息。


## <a name="azure-machine-learning"></a>Azure 机器学习


### <a name="error-code--4101"></a>错误代码：4101

- **消息**：`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **原因**：错误的格式或缺少属性的定义。

- **建议**：请检查是否已将活动定义为正确的数据。


### <a name="error-code--4110"></a>错误代码：4110

- **消息**：AzureMLExecutePipeline 活动在 JSON 中缺少 LinkedService 定义。

- **原因**：AzureMLExecutePipeline 活动定义不完整。

- **建议**：请检查输入 AzureMLExecutePipeline 活动 JSON 定义是否有链接的服务详细信息。


### <a name="error-code--4111"></a>错误代码：4111

- **消息**：`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **原因**：活动定义不正确。

- **建议**：请检查输入 AzureMLExecutePipeline 活动 JSON 定义是否有正确的链接服务详细信息。


### <a name="error-code--4112"></a>错误代码：4112

- **消息**：`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **原因**：错误的格式或缺少属性的定义。

- **建议**：请检查链接的服务定义是否有正确的数据。


### <a name="error-code--4121"></a>错误代码：4121

- **消息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**：用于访问 Azure ML 服务的凭据已过期。

- **建议**：请验证凭据是否有效，然后重试


### <a name="error-code--4122"></a>错误代码：4122

- **消息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**：AzureML 服务链接服务中提供的凭据无效或没有操作的权限。

- **建议**：请验证链接服务中的凭据是否有效，以及是否有权访问 AzureML 服务。


### <a name="error-code--4123"></a>错误代码：4123

- **消息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**：`Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **建议**：请检查活动属性的值，以匹配链接服务中指定的已发布 Azure ML 管道的预期负载。


### <a name="error-code--4124"></a>错误代码：4124

- **消息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**：已发布的 Azure ML 管道终结点不存在。

- **建议**：请验证 Azure ML 服务中是否存在链接的服务中指定的已发布 Azure ML 管道终结点。


### <a name="error-code--4125"></a>错误代码：4125

- **消息**：`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **原因**：Azure ML 服务上出现服务器错误。

- **建议**：请稍后重试。 如果问题仍然存在，请联系 Azure ML 服务团队以获得帮助。


### <a name="error-code--4126"></a>错误代码：4126

- **消息**：`AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **原因**：AzureML 管道运行失败。

- **建议**：请查看 AzureMLService 以了解更多错误 rsyslog 并修复 ML 管道


## <a name="custom"></a>自定义

下表适用于 Azure Batch。


### <a name="error-code--2500"></a>错误代码：2500

- **消息**：`Hit unexpected exception and execution failed.`

- **原因**：`Can't launch command, or the program returned an error code.`

- **建议**：确保可执行文件存在。 如果程序已启动, 请确保将*stdout*和*stderr*上传到存储帐户。 最好在代码中发出详细日志进行调试。


### <a name="error-code--2501"></a>错误代码：2501

- **消息**：`Cannot access user batch account; please check batch account settings.`

- **原因**：批处理访问密钥或池名称不正确。

- **建议**：验证链接的服务中的池名称和批处理访问密钥。


### <a name="error-code--2502"></a>错误代码：2502

- **消息**：`Cannot access user storage account; please check storage account settings.`

- **原因**：存储帐户名称或访问密钥不正确。

- **建议**：验证链接的服务中的存储帐户名称和访问密钥。


### <a name="error-code--2504"></a>错误代码：2504

- **消息**：`Operation returned an invalid status code 'BadRequest'.` 

- **原因**：自定义活动的 folderPath 中的文件太多。 ResourceFiles 的总大小不能超过32768个字符。

- **建议**：删除不必要的文件。 或对其进行压缩, 并添加解压缩命令来提取它们。 例如, 使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>错误代码：2505

- **消息**：`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **原因**：自定义活动只支持使用访问密钥的存储帐户。

- **建议**：请参阅错误说明。


### <a name="error-code--2507"></a>错误代码：2507

- **消息**：`The folder path does not exist or is empty: ....`

- **原因**：位于指定路径的存储帐户中不存在任何文件。

- **建议**：文件夹路径必须包含要运行的可执行文件。


### <a name="error-code--2508"></a>错误代码：2508

- **消息**：`There are duplicate files in the resource folder.`

- **原因**：具有相同名称的多个文件位于 folderPath 的不同子文件夹中。

- **建议**：自定义活动在 folderPath 下平展文件夹结构。  如果需要保留文件夹结构, 请压缩文件, 并使用解压缩命令将这些文件解压缩到 Azure Batch 中。 例如, 使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>错误代码：2509

- **消息**：`Batch   url ... is invalid; it must be in Uri format.` 

- **原因**：批处理 Url 必须与`https://mybatchaccount.eastus.batch.azure.com`

- **建议**：请参阅错误说明。


### <a name="error-code--2510"></a>错误代码：2510

- **消息**：`An   error occurred while sending the request.`

- **原因**：批处理 URL 无效。 

- **建议**：验证批处理 URL。


## <a name="hdinsight"></a>HDInsight

下表适用于 Spark、Hive、MapReduce、Pig 和 Hadoop 流式处理。


### <a name="error-code--2300"></a>错误代码：2300

- **消息**：`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **原因**：提供的群集 URI 无效。 

- **建议**：请确保未删除群集, 并且提供的 URI 是正确的。 在浏览器中打开 URI 时, 应会看到 Ambari UI。 如果群集位于虚拟网络中, 则 URI 应为专用 URI。 若要打开它, 请使用同一虚拟网络中的 VM。 有关详细信息, 请参阅[直接连接到 Apache Hadoop 服务](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)。

<br/>

- **消息**：`Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **原因**：作业提交超时。 

- **建议**：此问题可能是常规 HDInsight 连接或网络连接。 首先确认 HDInsight Ambari UI 可从任何浏览器获得。 确认凭据仍然有效。 如果使用自承载集成运行时 (IR), 请确保从安装自承载 IR 的 VM 或计算机中执行此操作。 然后尝试再次从数据工厂提交作业。 如果仍失败, 请与数据工厂团队联系以获取支持。


- **消息**：`Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **原因**：HDInsight 的凭据不正确或已过期。

- **建议**：更正凭据并重新部署链接服务。 首先, 请确保凭据在 HDInsight 上起作用, 方法是在任何浏览器上打开群集 URI 并尝试登录。 如果凭据不起作用, 则可以从 Azure 门户重置它们。

<br/>

- **消息**：`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **原因**：此错误来自 HDInsight。

- **建议**：此错误来自 HDInsight 群集。 有关详细信息, 请参阅[AMBARI UI 502 错误](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)、[连接到 spark Thrift 服务器时出现502错误](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)、 [502 连接到 spark Thrift 服务器的错误](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)和[排查应用程序网关中的网关错误](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)。

<br/>

- **消息**：`Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **原因**：同时提交到 HDInsight 的作业太多。

- **建议**：请考虑限制提交到 HDInsight 的并发作业的数目。 如果作业正在由同一活动提交, 则请参阅数据工厂活动并发性。 更改触发器，将并发管道运行分散到不同的时间。 如错误的建议, 请`templeton.parallellism.job.submit`参阅 HDInsight 文档进行调整。


### <a name="error-code--2303"></a>错误代码：2303

- **消息**：`Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **原因**：作业已提交到 HDInsight, 在 HDInsight 上失败。

- **建议**：已成功将作业提交到 HDInsight。 群集上的失败。 打开 HDInsight Ambari UI 中的作业和日志, 或从存储中打开该文件, 如错误消息所述。 文件显示错误详细信息。


### <a name="error-code--2310"></a>错误代码：2310

- **消息**：`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **原因**：提供的群集 URI 无效。 

- **建议**：请确保未删除群集, 并且提供的 URI 是正确的。 在浏览器中打开 URI 时, 应会看到 Ambari UI。 如果群集位于虚拟网络中, 则 URI 应为专用 URI。 若要打开它, 请使用同一虚拟网络中的 VM。 有关详细信息, 请参阅[直接连接到 Apache Hadoop 服务](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)。

<br/>

- **消息**：`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **原因**：此错误来自 HDInsight。

- **建议**：此错误来自 HDInsight 群集。 有关详细信息, 请参阅[AMBARI UI 502 错误](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)、[连接到 spark Thrift 服务器时出现502错误](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)、 [502 连接到 spark Thrift 服务器的错误](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)和[排查应用程序网关中的网关错误](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)。

<br/>

- **消息**：`java.lang.NullPointerException`

- **原因**：将作业提交到 Spark 群集时, 会发生此错误。 

- **建议**：此异常来自 HDInsight。 它隐藏了实际问题。 请联系 HDInsight 团队寻求支持。 为它们提供群集名称和活动运行时间范围。


### <a name="error-code--2347"></a>错误代码：2347

- **消息**：`Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **原因**：作业已提交到 HDInsight, 在 HDInsight 上失败。

- **建议**：已成功将作业提交到 HDInsight。 群集上的失败。 打开 HDInsight Ambari UI 中的作业和日志, 或从存储中打开该文件, 如错误消息所述。 文件显示错误详细信息。


### <a name="error-code--2328"></a>错误代码：2328

- **消息**：`Internal server error occurred while processing the request. Please retry the request or contact support. `

- **原因**：此错误按需在 HDInsight 中发生。

- **建议**：当 HDInsight 设置失败时, 此错误来自 HDInsight 服务。 联系 HDInsight 团队并提供按需群集名称。



## <a name="web-activity"></a>Web 活动

### <a name="error-code--2108"></a>错误代码：2108

- **消息**：`Invalid HttpMethod: '...'.`

- **原因**：Web 活动不支持在活动有效负载中指定的 HTTP 方法。

- **建议**：支持的 HTTP 方法有 PUT、POST、GET 和 DELETE。

<br/>

- **消息**：`Invalid Server Error 500.`

- **原因**：终结点上的内部错误。

- **建议**：使用 Fiddler 或 Postman 检查 URL 的功能。

<br/>

- **消息**：`Unauthorized 401.`

- **原因**：请求上缺少有效身份验证。

- **建议**：令牌可能已过期。 提供有效的身份验证方法。 使用 Fiddler 或 Postman 检查 URL 的功能。

<br/>

- **消息**：`Forbidden 403.`

- **原因**：缺少所需的权限。

- **建议**：检查用户对所要访问的资源的权限。 使用 Fiddler 或 Postman 检查 URL 的功能。

<br/>

- **消息**：`Bad Request 400.`

- **原因**：无效的 HTTP 请求。

- **建议**： 检查请求的 URL、谓词和正文。 使用 Fiddler 或 Postman 来验证该请求。

<br/>

- **消息**：`Not found 404.` 

- **原因**：找不到该资源。   

- **建议**：使用 Fiddler 或 Postman 来验证该请求。

<br/>

- **消息**：`Service unavailable.`

- **原因**：服务不可用。

- **建议**：使用 Fiddler 或 Postman 来验证该请求。

<br/>

- **消息**：`Unsupported Media Type.`

- **原因**：内容类型与 Web 活动正文不匹配。

- **建议**：指定匹配有效负载格式的内容类型。 使用 Fiddler 或 Postman 来验证该请求。

<br/>

- **消息**：`The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **原因**：资源不可用。 

- **建议**：使用 Fiddler 或 Postman 检查终结点。

<br/>

- **消息**：`The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **原因**：请求中指定了不正确的 Web 活动方法。

- **建议**：使用 Fiddler 或 Postman 检查终结点。

<br/>

- **消息**：`invalid_payload`

- **原因**：Web 活动正文不正确。

- **建议**：使用 Fiddler 或 Postman 检查终结点。


### <a name="error-code--2208"></a>错误代码：2208

- **消息**：`Invoking Web Activity failed with HttpStatusCode - {0}.`

- **原因**：目标服务返回失败状态。

- **建议**：使用 Fiddler/Postman 验证该请求。


### <a name="error-code--2308"></a>错误代码：2308

- **消息**：`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **原因**：此错误可能有多种原因，如网络连接性、DNS 失败、服务器证书验证或超时。

- **建议**：使用 Fiddler/Postman 验证该请求。


若要使用 Fiddler 创建监视的 web 应用程序的 HTTP 会话, 请执行以下操作:

1. 下载、安装并打开[Fiddler](https://www.telerik.com/download/fiddler)。

1. 如果你的 web 应用程序使用 https, 请跳到**Tools** > **Fiddler Options** > **https**。 选择 "**捕获 Https 连接**" 和 "**解密 https 通信**"。 
   
   ![Fiddler 选项](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 如果你的应用程序使用 SSL 证书, 请将 Fiddler 证书添加到你的设备。 中转到**Tools** > **Fiddler Options** > **HTTPS** **操作** **将根证书导出到桌面。**  >  > 

1. 转到**文件** > **捕获流量**关闭捕获。 或按**F12**。

1. 清除浏览器的缓存, 以便删除所有已缓存的项, 并且必须再次下载。

1. 创建请求: 

   a. 选择 "**编辑器**" 选项卡。

   b. 设置 HTTP 方法和 URL。

   c. 如果需要, 请添加标头和请求正文。

   d. 选择“执行”。

9. 再次打开流量捕获, 并在页面上完成有问题的事务。

10. 中转到 "**文件** > " "**保存** > **所有会话**"。

有关详细信息, 请参阅[Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)入门。

## <a name="next-steps"></a>后续步骤

有关故障排除的详细信息, 请尝试以下资源:

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)



