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
ms.openlocfilehash: f35a3567ae4ae7c3e2d59f776d3a3bc00ec2be3e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142380"
---
# <a name="troubleshoot-azure-data-factory"></a>排查 Azure 数据工厂问题

本文探讨了 Azure 数据工厂中的外部控制活动的常见故障排除方法。

## <a name="connector-and-copy-activity"></a>连接器和复制活动

有关连接器问题 (例如, 使用复制活动时遇到错误), 请参阅对[Azure 数据工厂连接器进行故障排除](connector-troubleshoot-guide.md)。

## <a name="azure-databricks"></a>Azure Databricks

| 错误代码 | 错误消息                                          | 描述                             | 建议                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | 错误403。                                                    | Databricks 访问令牌已过期。                         | 默认情况下, Databricks 访问令牌的有效期为90天。  创建新的令牌并更新链接的服务。 |
| 3201           | 缺少必填字段: settings. notebook_task. notebook_path | 错误的创作:未正确指定笔记本路径。 | 在 Databricks 活动中指定笔记本路径。 |
| 3201           | 群集   ... 不存在。                                 | 创作错误:Databricks 群集不存在或已被删除。 | 验证 Databricks 群集是否存在。 |
| 3201           | Python 文件 URI 无效 ...请访问 Databricks 用户指南以获取支持的 URI 方案。 | 创作不当。                                                | 请为工作区寻址方案指定绝对路径, 或者`dbfs:/folder/subfolder/foo.py`为存储在 Databricks 文件系统中的文件指定绝对路径。 |
| 3201           | {0}LinkedService 应将 domain 和 accessToken 作为必需属性。 | 创作不当。                                                | 验证[链接的服务定义](compute-linked-services.md#azure-databricks-linked-service)。 |
| 3201           | {0}LinkedService 应指定要创建的现有群集 ID 或新群集信息。 | 创作不当。                                                | 验证[链接的服务定义](compute-linked-services.md#azure-databricks-linked-service)。 |
| 3201           | 节点类型 Standard_D16S_v3 不受支持。 支持的节点类型: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2,Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s,Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | 创作不当。                                                | 请参阅错误消息。                                          |
| 3201           | Notebook_path 无效: ...目前仅支持绝对路径。 路径必须以 "/" 开头。 | 创作不当。                                                | 请参阅错误消息。                                          |
| 3202           | 过去3600秒内已创建1000个作业, 超过了速率限制: 1000每3600秒创建一次作业。 | 一小时内运行的 Databricks 太多。                         | 检查使用此 Databricks 工作区的所有管道的作业创建速率。  如果管道启动了太多的 Databricks, 则将一些管道迁移到新的工作区。 |
| 3202           | 无法分析请求对象:应为 JSON 映射字段 base_parameters 设置 "key" 和 "value", 获取了 "key:" ... "" 是. | 创作错误:没有为参数提供值。         | 检查管道 JSON 并确保 baseParameters 笔记本中的所有参数都指定一个非空值。 |
| 3202           | 用户: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}`无权访问群集。 | 不允许生成访问令牌的用户访问在链接服务中指定的 Databricks 群集。 | 确保用户在工作区中具有所需的权限。   |
| 3203           | 群集处于终止状态, 不可用于接收作业。 请修复群集或稍后重试。 | 群集已终止。    对于交互式群集, 这可能是一个争用情况。 | 避免此错误的最佳方式是使用作业群集。             |
| 3204           | 作业执行失败。  | 错误消息指示各种问题, 例如意外群集状态或特定活动。 通常不会显示任何错误消息。                                                          | 不可用                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

下表适用于 U SQL。

| 错误代码         | 错误消息                                                | 描述                                          | 建议                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | 访问令牌来自错误的租户。                    | Azure Active Directory (Azure AD) 租户不正确。                                         | 用于访问 Azure Data Lake Analytics 的服务主体属于另一个 Azure AD 租户。 在与 Data Lake Analytics 帐户相同的租户中创建新的服务主体。 |
| 2711、2705、2704 | 禁止. ACL 验证失败。 资源不存在, 或者用户无权执行所请求的操作。<br/><br/>用户无法访问 Data Lake Store。  <br/><br/>用户无权使用 Data Lake Analytics。 | 服务主体或证书无权访问存储中的文件。 | 确保用户为 Data Lake Analytics 作业提供的服务主体或证书可以访问根文件夹中的 Data Lake Analytics 帐户和默认 Data Lake Storage 实例。 |
| 2711                 | 找不到 "Azure Data Lake Store" 文件或文件夹。       | 到 U SQL 文件的路径错误, 或链接的服务凭据没有访问权限。 | 验证在链接服务中提供的路径和凭据。 |
| 2707                 | 无法解析 AzureDataLakeAnalytics 的帐户。 请检查 "AccountName" 和 "DataLakeAnalyticsUri"。 | 链接的服务中的 Data Lake Analytics 帐户错误。                  | 验证是否提供了正确的帐户。             |
| 2703                 | 错误 Id:E_CQO_SYSTEM_INTERNAL_ERROR (或以 "错误 Id:" 开头的任何错误)。 | 错误来自 Data Lake Analytics。                                    | 如示例中所示的错误表示作业已提交到 Data Lake Analytics, 但脚本失败。 在 Data Lake Analytics 中进行调查。 在门户中, 中转到 Data Lake Analytics 帐户, 并使用数据工厂活动运行 ID (不是管道运行 ID) 查找作业。 其中提供了有关错误的详细信息, 并将帮助你进行故障排除。 如果解决方法不清楚, 请联系 Data Lake Analytics 支持团队并提供作业 URL, 其中包括你的帐户名称和作业 ID。 |
| 2709                 | 目前无法接受你的作业。 你的帐户的最大排队作业数为200。 | 此错误是由 Data Lake Analytics 上的限制引起的。                                           | 通过更改活动的数据工厂触发器和并发设置, 将提交的作业数减少到 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。 |
| 2709                 | 此作业被拒绝, 因为它需要24个澳大利亚。 此帐户的管理员定义策略阻止作业使用5个以上的澳大利亚。 | 此错误是由 Data Lake Analytics 上的限制引起的。                                           | 通过更改活动的数据工厂触发器和并发设置, 将提交的作业数减少到 Data Lake Analytics。 或增加 Data Lake Analytics 的限制。 |



## <a name="azure-functions"></a>Azure Functions

| 错误代码 | 错误消息                           | 描述                                                  | 建议                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | 响应内容不是有效的 JObject。 | 调用的 Azure 函数未在响应中返回 JSON 有效负载。 数据工厂中的 Azure function 活动仅支持 JSON 响应内容。 | 更新 Azure 函数以返回有效的 JSON 有效负载。 例如, C#函数可返回`(ActionResult)new<OkObjectResult("{` \"Id\":\"123。\"`}");` |
| 3600         | 无效的 HttpMethod: "..."。               | Azure function 活动不支持活动有效负载中指定的 HTTP 方法。 | 使用受支持的 HTTP 方法, 例如 PUT、POST、GET、DELETE、OPTIONS、HEAD 或 TRACE。 |



## <a name="custom"></a>自定义

下表适用于 Azure Batch。

| 错误代码 | 错误消息                                                | 描述                                                  | 建议                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | 遇到意外的异常，且执行失败。             | 无法启动命令, 或程序返回了错误代码。 | 确保可执行文件存在。 如果程序已启动, 请确保将*stdout*和*stderr*上传到存储帐户。 最好在代码中发出详细日志进行调试。 |
| 2501         | 无法访问用户批处理帐户;请检查 batch 帐户设置。 | 批处理访问密钥或池名称不正确。            | 验证链接的服务中的池名称和批处理访问密钥。 |
| 2502         | 无法访问用户存储帐户;请检查存储帐户设置。 | 存储帐户名称或访问密钥不正确。       | 验证链接的服务中的存储帐户名称和访问密钥。 |
| 2504         | 操作返回的状态代码 "BadRequest" 无效。     | 自定义活动的 folderPath 中的文件太多。 ResourceFiles 的总大小不能超过32768个字符。 | 删除不必要的文件。 或对其进行压缩, 并添加解压缩命令来提取它们。 例如, 使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | 除非使用帐户密钥凭据，否则无法创建共享访问签名。 | 自定义活动只支持使用访问密钥的存储帐户。 | 请参阅错误说明。                                            |
| 2507         | 文件夹路径不存在或为空: ...            | 位于指定路径的存储帐户中不存在任何文件。       | 文件夹路径必须包含要运行的可执行文件。 |
| 2508         | 资源文件夹中有重复的文件。               | 具有相同名称的多个文件位于 folderPath 的不同子文件夹中。 | 自定义活动在 folderPath 下平展文件夹结构。  如果需要保留文件夹结构, 请压缩文件, 并使用解压缩命令将这些文件解压缩到 Azure Batch 中。 例如, 使用`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | 批处理 url ... 无效;它必须采用 Uri 格式。         | 批处理 Url 必须与`https://mybatchaccount.eastus.batch.azure.com` | 请参阅错误说明。                                            |
| 2510         | 发送请求时出错。               | 批处理 URL 无效。                                         | 验证批处理 URL。                                            |

## <a name="hdinsight"></a>HDInsight

下表适用于 Spark、Hive、MapReduce、Pig 和 Hadoop 流式处理。

| 错误代码 | 错误消息                                                | 描述                                                  | 建议                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300、2310 | Hadoop 作业提交失败。 错误：无法解析远程名称”的问题。 <br/><br/>找不到群集。 | 提供的群集 URI 无效。                              | 请确保未删除群集, 并且提供的 URI 是正确的。 在浏览器中打开 URI 时, 应会看到 Ambari UI。 如果群集位于虚拟网络中, 则 URI 应为专用 URI。 若要打开它, 请使用同一虚拟网络中的 VM。 有关详细信息, 请参阅[直接连接到 Apache Hadoop 服务](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)。 |
| 2300         | Hadoop 作业提交失败。 作业: ...，群集: .../。 错误：任务已取消。 | 作业提交超时。                         | 此问题可能是常规 HDInsight 连接或网络连接。 首先确认 HDInsight Ambari UI 可从任何浏览器获得。 确认凭据仍然有效。 如果使用自承载集成运行时 (IR), 请确保从安装自承载 IR 的 VM 或计算机中执行此操作。 然后尝试再次从数据工厂提交作业。 如果仍失败, 请与数据工厂团队联系以获取支持。 |
| 2300         | 未授权: Ambari 用户名或密码不正确  <br/><br/>未授权: 用户管理员在 Ambari 中被锁定。   <br/><br/>403-禁止访问:访问被拒绝。 | HDInsight 的凭据不正确或已过期。 | 更正凭据并重新部署链接服务。 首先, 请确保凭据在 HDInsight 上起作用, 方法是在任何浏览器上打开群集 URI 并尝试登录。 如果凭据不起作用, 则可以从 Azure 门户重置它们。 |
| 2300、2310 | 502 - Web 服务器在充当网关或代理服务器时收到了无效响应。       <br/>网关错误。 | 此错误来自 HDInsight。                               | 此错误来自 HDInsight 群集。 有关详细信息, 请参阅[AMBARI UI 502 错误](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)、[连接到 spark Thrift 服务器时出现502错误](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)、 [502 连接到 spark Thrift 服务器的错误](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)和[排查应用程序网关中的网关错误](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)。 |
| 2300         | Hadoop 作业提交失败。 作业: ..., 群集: ...错误: {\"错误\":\"无法为提交作业请求提供服务，因为 templeton 服务正忙于处理过多的提交作业请求。 请等待一段时间，然后重试该操作。 请参阅配置 templeton 以配置并发请求。  <br/><br/>Hadoop 作业提交失败。 作业:161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, 群集: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`。   错误: {\"错误\":\"java.io.IOException: org.apache.hadoop.yarn.exceptions.YarnException:无法将 application_1561147195099_3730 提交到 YARN: org.apache.hadoop.security.AccessControlException:队列 root.joblauncher 已包含 500 个应用程序，无法接受应用程序提交: application_1561147195099_3730\ | 同时提交到 HDInsight 的作业太多。 | 请考虑限制提交到 HDInsight 的并发作业的数目。 如果作业正在由同一活动提交, 则请参阅数据工厂活动并发性。 更改触发器，将并发管道运行分散到不同的时间。 如错误的建议, 请`templeton.parallellism.job.submit`参阅 HDInsight 文档进行调整。 |
| 2303、2347 | Hadoop 作业失败并返回了退出代码 "5"。 有关更多详细信息，请参阅“wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr”。  <br/><br/>Hive 执行失败并返回了错误代码 'UserErrorHiveOdbcCommandExecutionFailure'。   有关更wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out多详细信息, 请参阅 ""。 | 作业已提交到 HDInsight, 在 HDInsight 上失败。 | 已成功将作业提交到 HDInsight。 群集上的失败。 打开 HDInsight Ambari UI 中的作业和日志, 或从存储中打开该文件, 如错误消息所述。 文件显示错误详细信息。 |
| 2328         | 处理请求时发生内部服务器错误。 请重试请求或联系支持人员。 | 此错误按需在 HDInsight 中发生。                              | 当 HDInsight 设置失败时, 此错误来自 HDInsight 服务。 联系 HDInsight 团队并提供按需群集名称。 |
| 2310         | java.lang.NullPointerException                               | 将作业提交到 Spark 群集时, 会发生此错误。      | 此异常来自 HDInsight。 它隐藏了实际问题。 请联系 HDInsight 团队寻求支持。 为它们提供群集名称和活动运行时间范围。 |
|              | 所有其他错误                                             |                                                              | 请参阅使用 HDInsight 和[HDINSIGHT 常见问题](https://hdinsight.github.io/)[进行故障排除](../hdinsight/hdinsight-troubleshoot-guide.md)。 |



## <a name="web-activity"></a>Web 活动

| 错误代码 | 错误消息                                                | 描述                                                  | 建议                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | 无效的 HttpMethod: "..."。                                    | Web 活动不支持在活动有效负载中指定的 HTTP 方法。 | 支持的 HTTP 方法有 PUT、POST、GET 和 DELETE。 |
| 2108         | 无效的服务器错误500。                                     | 终结点上的内部错误。                               | 使用 Fiddler 或 Postman 检查 URL 的功能。 |
| 2108         | 未经授权的401。                                             | 请求上缺少有效身份验证。                      | 令牌可能已过期。 提供有效的身份验证方法。 使用 Fiddler 或 Postman 检查 URL 的功能。 |
| 2108         | 禁用403。                                                | 缺少所需的权限。                                 | 检查用户对所要访问的资源的权限。 使用 Fiddler 或 Postman 检查 URL 的功能。  |
| 2108         | 错误的请求400。                                              | 无效的 HTTP 请求。                                         | 检查请求的 URL、谓词和正文。 使用 Fiddler 或 Postman 来验证该请求。  |
| 2108         | 未找到404。                                                | 找不到该资源。                                       | 使用 Fiddler 或 Postman 来验证该请求。  |
| 2108         | 服务不可用。                                          | 服务不可用。                                       | 使用 Fiddler 或 Postman 来验证该请求。  |
| 2108         | 媒体类型不受支持。                                       | 内容类型与 Web 活动正文不匹配。           | 指定匹配有效负载格式的内容类型。 使用 Fiddler 或 Postman 来验证该请求。 |
| 2108         | 你要查找的资源已被删除, 其名称已更改, 或者暂时不可用。 | 资源不可用。                                | 使用 Fiddler 或 Postman 检查终结点。 |
| 2108         | 正在查找的页面无法显示, 因为正在使用无效方法 (HTTP 谓词)。 | 请求中指定了不正确的 Web 活动方法。   | 使用 Fiddler 或 Postman 检查终结点。 |
| 2108         | invalid_payload                                              | Web 活动正文不正确。                       | 使用 Fiddler 或 Postman 检查终结点。 |

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



