---
title: Azure 数据工厂进行故障排除 |Microsoft Docs
description: Azure 数据工厂的故障排除。 对所有外部，控制活动的公共文档。
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshoot
ms.subservice: troubleshoot
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 8d6ab565098e1ea40ede5c650f05e670a1edc7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452678"
---
# <a name="troubleshooting-azure-data-factory"></a>Azure 数据工厂进行故障排除
本文列出了常见疑难解答问题。

- [Azure Databricks notebook、 jar (python）](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Azure Functions](#azure-functions)
- [自定义 (Azure Batch)](#custom-azure-batch)
- [HDInsight （Spark、 Hive、 MapReduce、 Pig、 Hadoop 流式处理）](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Web 活动](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| 错误代码 | 错误消息                                          | 问题描述                             | 可能的修复 / 建议的操作                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | 403 错误                                                    | Databricks 访问令牌已过期。                         | 默认情况下，Databricks 访问令牌的有效期为 90 天。  请创建新的令牌，并更新链接的服务。 |
| 3201           | 缺少必填的字段： settings.task.notebook_task.notebook_path | 创作的错误：未正确指定 notebook 路径。 | 请指定 Databricks 活动中的 notebook 路径。 |
| 3201           | 群集...不存在                                 | 创作错误：Databricks 群集不存在或已被删除。 | 请验证在 Databricks 群集存在。 |
| 3201           | 无效的 python 文件 URI:...请访问 Databricks 用户指南以获取支持的 URI 方案。 | 创作的错误                                                | 指定工作区寻址方案的绝对路径或为 DBFS 存储文件的"dbfs:/folder/subfolder/foo.py"。 |
| 3201           | {0}   链接服务应具有与域和 accessToken 必需的属性 | 创作的错误                                                | 请验证[链接服务定义](compute-linked-services.md#azure-databricks-linked-service)。 |
| 3201           | {0}   链接服务应指定现有群集 Id 或创建新群集信息 | 创作的错误                                                | 请验证[链接服务定义](compute-linked-services.md#azure-databricks-linked-service)。 |
| 3201           | 不支持节点类型 Standard_D16S_v3。 支持的节点类型： Standard_DS3_v2、 Standard_DS4_v2、 Standard_DS5_v2、 Standard_D8s_v3、 Standard_D16s_v3、 Standard_D32s_v3、 Standard_D64s_v3、 标准 d3 v2、 Standard_D8_v3、 Standard_D16_v3、 Standard_D32_v3、 Standard_D64_v3、 标准 d12 v2、 标准 d13 v2，标准 d14 v2、 Standard_D15_v2、 Standard_DS12_v2、 Standard_DS13_v2、 Standard_DS14_v2、 Standard_DS15_v2、 Standard_E8s_v3、 Standard_E16s_v3、 Standard_E32s_v3、 Standard_E64s_v3、 Standard_L4s、 Standard_L8s、 Standard_L16s、 Standard_L32s，Standard_F4s、 Standard_F8s、 Standard_F16s、 Standard_H16、 Standard_F4s_v2、 Standard_F8s_v2、 Standard_F16s_v2、 Standard_F32s_v2、 Standard_F64s_v2、 Standard_F72s_v2、 Standard_NC12、 Standard_NC24、 Standard_NC6s_v3、 Standard_NC12s_v3、 Standard_NC24s_v3、 Standard_L8s_v2、 Standard_L16s_v2、 Standard_L32s_v2、 Standard_L64s_v2、 Standard_L80s_v2 | 创作的错误                                                | 错误消息，请参阅                                          |
| 3201           | 无效 notebook_path:...目前支持仅绝对路径。 路径必须以 /。 | 创作的错误                                                | 错误消息，请参阅                                          |
| 3202           | 已在过去 3600 秒，超过速率限制中创建的 1000 个作业： 1000 作业创建 3600 秒数。 | 太多 Databricks 运行在一小时。                         | 检查所有管道都使用此 Databricks 工作区为作业创建速率。  如果管道启动过多 Databricks 运行聚合中，将一些管道迁移到新的工作区中。 |
| 3202           | 无法分析请求对象：预期的密钥和值设置为 JSON 映射字段 base_parameters，获得键:"..." 改为。 | 创作错误：没有为参数提供值         | 检查管道 json，并确保 Notebook baseParameters 中的所有参数都具有指定的非空值。 |
| 3202           | 用户：SimpleUserContext {userId =...，名称 =user@company.com，orgId =...} 未授权访问群集 | 生成访问令牌的用户不被允许访问指定链接服务中的 Databricks 群集。 | 确保用户在工作区中具有所需的权限。   |
| 3203           | 群集处于终止状态，不可用于接收作业。 请解决群集或稍后重试。 | 已终止群集。    对于交互式群集，这可能是一个争用条件。 | 若要避免此问题，最好是使用作业群集。             |
| 3204           | 作业执行失败。 可以有任意数量的错误消息，从意外的群集状态到特定于活动的消息。  最常见根本没有错误消息。 | 不适用                                                          | 不适用                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| 错误代码         | 错误消息                                                | 问题描述                                          | 可能的修复 / 建议的操作                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | 访问令牌是从错误的租户                    | 不正确的 AAD 租户                                         | 用于访问 ADLA 服务主体属于另一个 AAD 租户。 请在与 ADLA 帐户相同的租户中创建新的服务主体。 |
| 2711,   2705,   2704 | 禁止访问。 ACL 验证失败。 资源不存在或用户无权执行请求的操作<br/><br/>用户不能访问到 datalake store  <br/><br/>用户无权到 data lake analytics | 服务主体或提供的证书不到存储中的文件具有访问权限 | 请确保服务主体或它们的 ADLA 作业提供的证书从根文件夹为其有权访问 ADLA 帐户和默认 ADLS 存储。 |
| 2711                 | 找不到 Azure Data Lake Store 文件或文件夹       | USQL 文件的路径是错误或凭据不具有访问权限的链接的服务 | 请验证路径和链接服务中提供的凭据 |
| 2707                 | 无法解析 AzureDataLakeAnalytics 的帐户。 请检查 AccountName 和 DataLakeAnalyticsUri。 | 链接服务的 ADLA 帐户是不正确                  | 请验证提供正确的帐户             |
| 2703                 | 错误 Id:E_CQO_SYSTEM_INTERNAL_ERROR 或任何错误开头为"错误 Id:" | 错误来自 ADLA                                    | 任何错误看起来像此示例意味着作业已提交到 ADLA 和那里的脚本失败。 必须 ADLA 上完成调查。 如果打开门户并导航到 ADLA 帐户，可以通过使用 ADF 活动运行 Id （不管道运行 Id） 查找该作业。 存在该作业将包含有关错误的详细信息，并将有助于进行故障排除。 如果解析不明确，请联系 ADLA 支持团队，并提供作业 URL，其中包括您的帐户名和作业 id。 |
| 2709                 | 此时，我们不能接受你的作业。 最大为你的帐户已排队作业数为 200。 | ADLA 上限制                                           | 将已提交的作业的数目减少到 ADLA 中，通过更改 ADF 触发器和并发设置的活动，或增加 ADLA 上的限制 |
| 2709                 | 此作业被拒绝，因为它需要 24 澳大利亚和此帐户具有管理员定义的策略，以防止作业使用多个 5 Au。 | ADLA 上限制                                           | 将已提交的作业的数目减少到 ADLA 中，通过更改 ADF 触发器和并发设置的活动，或增加 ADLA 上的限制 |



## <a name="azure-functions"></a>Azure Functions

| 错误代码 | 错误消息                           | 描述                                                  | 可能的修复 / 建议的操作                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | 响应内容不是有效的 JObject | 这意味着获取调用 Azure 函数未在响应中返回 JSON 有效负载。 ADF Azure 函数活动仅支持 JSON 响应内容。 | 更新 Azure 函数以返回有效的 JSON 有效负载，例如C#函数可以返回 (ActionResult) 新 < okobjectresult 则 ("{`\"Id\":\"123\"`}"); |
| 3600         | 无效的 HttpMethod:...。               | 这意味着 Azure 函数活动不支持活动负载中指定的 Http 方法。 | 支持的 Http 方法包括：  <br/>PUT、 POST、 GET、 DELETE、 选项、 HEAD、 跟踪 |



## <a name="custom-azure-batch"></a>自定义 (Azure Batch)
| 错误代码 | 错误消息                                                | 描述                                                  | 可能的修复 / 建议的操作                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | 遇到意外的异常和失败的执行。             | 无法启动命令或返回了错误代码的程序。 | 检查可执行文件存在。 如果启动程序，检查 stdout.txt 和 stderr.txt 上传到存储帐户。 它是一个好办法发出用于调试在代码中的详细日志。 |
| 2501         | 无法访问用户 batch 帐户，请查看 batch 帐户设置。 | 不正确的批处理提供访问密钥或池的名称。            | 需要验证池名称和批处理中的链接服务的访问密钥。 |
| 2502         | 可以不访问用户存储帐户，请检查存储帐户设置。 | 不正确的存储帐户名称或访问密钥提供。       | 需要验证链接的服务中存储帐户名称和访问密钥。 |
| 2504         | 操作返回了无效的状态代码错误的请求     | FolderPath 中的文件太多如果自定义活动。  （resourceFiles 的总大小不能为多个 32768 个字符）。 | 删除不必要的文件或压缩它们，并添加解压缩命令来提取，例如： powershell.exe-nologo noprofile-命令"& {添加类型-A System.IO.Compression.FileSystem';  [IO.Compression.ZipFile]::ExtractToDirectory （$zipFile，$folder）;}" ; $folder\yourProgram.exe |
| 2505         | 无法创建共享访问签名，除非使用帐户密钥凭据。 | 自定义活动仅支持使用访问密钥的存储帐户。 | 说明，请参阅                                            |
| 2507         | 文件夹路径不存在或为空:...            | 指定路径处的存储帐户中没有文件。       | FolderPath 必须包含你想要运行的可执行文件。 |
| 2508         | 在资源文件夹中有重复的文件。               | 有的 folderPath 不同子文件夹中具有相同名称的多个文件。 | 自定义活动进行平展处理在 folderPath 下的文件夹结构。  如果需要保留文件夹结构，zip 文件并将其提取 Azure Batch 上具有解压缩命令，例如： powershell.exe-nologo noprofile-命令"& {添加类型-A System.IO.Compression.FileSystem';  [IO.Compression.ZipFile]::ExtractToDirectory （$zipFile，$folder）;}" ;  $folder\yourProgram.exe |
| 2509         | 批处理 url...是无效的它必须为 Uri 格式。         | 批处理的 Url 必须是类似于 https://mybatchaccount.eastus.batch.azure.com | 说明，请参阅                                            |
| 2510         | 发送请求时出错。               | 批处理 URL 无效                                         | 验证批处理 URL。                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight （Spark、 Hive、 MapReduce、 Pig、 Hadoop 流式处理）

| 错误代码 | 错误消息                                                | 描述                                                  | 可能的修复 / 建议的操作                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | 无法提交 Hadoop 作业。 错误：无法解析远程名称”的问题。 <br/><br/>找不到群集。 | 提供的群集 URI 无效                              | 请确保尚未删除群集，并且提供的 URI 正确。 可以在任何浏览器中打开 URI，您应看到 Ambari UI。 如果群集是在 vNet 中，则 URI 应为私有的 URI，并且应尝试从属于同一个 vNet 的 VM 中打开它。 有关详细信息[在 HDInsight 中的虚拟网络](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)。 |
| 2300         | 无法提交 Hadoop 作业。 作业:...，群集:.../。 错误：任务已取消。 | 作业的提交操作已超时。                         | 这可能是常规 HDInsight 连接问题或网络连接问题。 首先请确认 HDInsight Ambari UI 是可通过任何浏览器并且你的凭据仍有效。 请务必执行此操作从 VM/计算机使用自承载 ir。 如果安装自承载的 IR 然后尝试重新提交从 ADF 作业。 如果仍然失败，请与支持的 ADF 团队联系。 |
| 2300         | 未经授权: Ambari 用户名或密码不正确  <br/><br/>未经授权: 用户管理员被锁定在 Ambari 中   <br/><br/>403-禁止访问：访问被拒绝 | 为 HDInsight 提供的凭据不正确，或者已过期 | 请更正它们并重新部署链接的服务。 请确保凭据使用 HDInsight 上首先通过打开群集 URI 的任何浏览器并尝试登录。 如果它们不起作用，您可以从 Azure 门户重置它们。 |
| 2300,   2310 | 502-web 服务器作为网关或代理服务器时收到了无效响应       <br/>错误的网关 | 错误来自 HDInsight                               | 此错误即将从 HDInsight 群集。 请参阅[HDInsight 故障排除程序](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)与常见的错误。    <br/>对于 Spark 群集它可能还导致因[这](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)。 <br/><br/>[其他链接](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2300         | 无法提交 Hadoop 作业。 作业:...，群集:...错误: {\"错误\":\"无法如 templeton 服务正忙于处理请求过多提交作业提交作业请求提供服务。 请等待一些时间才会重试该操作。 请参阅配置 templeton.parallellism.job.submit，可以配置并发请求。 \  <br/><br/>无法提交 Hadoop 作业。 作业：161da5d4-6fa8-4ef4-a240-6b6428c5ae2f，群集： https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/ 。   错误: {\"错误\":\"java.io.IOException: org.apache.hadoop.yarn.exceptions.YarnException:未能提交到 YARN application_1561147195099_3730: org.apache.hadoop.security.AccessControlException:队列 root.joblauncher 已有 500 个应用程序，将无法接受的应用程序提交： application_1561147195099_3730\ | 太多的作业是在同时提交到 HDInsight | 请考虑限制提交到 HDI 的并发作业数。 请参阅 ADF 活动并发，如果他们要提交的同一个活动。 更改触发器，以便随着时间的推移分散在并发的管道运行。 另请参阅 HDInsight 文档以便调整"templeton.parallellism.job.submit"，如错误所示。 |
| 2303,   2347 | Hadoop 作业失败，退出代码为"5"。 请参阅wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr的更多详细信息。  <br/><br/>Hive 执行失败，错误代码 UserErrorHiveOdbcCommandExecutionFailure。   请参阅wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out的更多详细信息 | 作业已提交到 HDInsight，和它在 HDInsight 上失败 | 作业已成功提交到 HDInsight。 它未在群集上。 请打开 HDInsight Ambari UI 上的作业，打开日志，或者作为错误消息指出从存储打开文件。错误的详细信息将在该文件中。 |
| 2328         | 处理请求时出现内部服务器错误。 请重试请求，或联系支持人员 | 按需 HDInsight 上发生的情况。                              | HDInsight 预配失败时，此错误将来自 HDInsight 服务。 请与 HDInsight 团队联系并向其按需群集名称。 |
| 2310         | java.lang.NullPointerException                               | 将作业提交到 Spark 群集时出错了      | 此异常来自 HDInsight 和隐藏实际问题。   请联系 HDInsight 团队以获取支持，并向他们提供群集名称和活动运行时间范围。 |
|              | 所有其他错误                                             |                                                              | 请参阅[HDInsight 故障排除工具](../hdinsight/hdinsight-troubleshoot-guide.md)和[HDInsight 常见问题](https://hdinsight.github.io/) |



## <a name="web-activity"></a>Web 活动

| 错误代码 | 错误消息                                                | 描述                                                  | 可能的修复 / 建议的操作                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | 无效的 HttpMethod:...。                                    | 这意味着 Web 活动不支持活动负载中指定的 Http 方法。 | 支持的 Http 方法包括： <br/>PUT、 POST、 获取、 删除 |
| 2108         | 无效的服务器错误 500                                     | 在终结点上的内部错误                               | 检查 （使用 Fiddler/Postman) 在 URL 上的功能：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 未经授权的 401                                             | 请求缺少有效的身份验证                      | 提供有效的身份验证方法 （令牌可能已过期）。   <br/><br/>检查 （使用 Fiddler/Postman) 在 URL 上的功能：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 403 禁止访问                                                | 缺少所需的权限                                 | 检查用户访问资源的权限。   <br/><br/>检查 （使用 Fiddler/Postman) 在 URL 上的功能：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 400 错误的请求                                              | 无效的 Http 请求                                         | 检查 URL、 谓词和请求的正文。   <br/><br/>使用 Fiddler/Postman 来验证请求：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 未找到 404                                                | 找不到资源                                       | 使用 Fiddler/Postman 来验证请求：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 服务不可用                                          | 服务不可用                                       | 使用 Fiddler/Postman 来验证请求：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 不支持的媒体类型                                       | 使用 Web 活动主体不匹配的内容类型           | 指定正确的内容的类型相匹配的负载格式使用 Fiddler/Postman 来验证请求：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 要查看的资源已删除，已更改，其名称，或暂时不可用。 | 资源不可用                                | 使用 Fiddler/Postman 检查终结点：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 无法显示所查找的页面，因为正在使用无效的方法 （HTTP 谓词）。 | 在请求中指定了不正确的 Web 活动方法   | 使用 Fiddler/Postman 检查终结点：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | Web 活动的正文不正确                       | 使用 Fiddler/Postman 检查终结点：[如何使用 Fiddler 创建 HTTP 会话](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>如何使用 Fiddler 创建 HTTP 会话的监视的 web 应用程序

1. 下载并安装 [Fiddler](https://www.telerik.com/download/fiddler)

2. 如果 web 应用程序使用 HTTPS:

   1. 打开 Fiddler

   2. 转到**工具 > Fiddler 选项**和选择如以下屏幕截图。 

      ![fiddler-options](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. 如果你的应用程序使用 SSL 证书，还必须将 Fiddler 证书添加到你的设备中。

4. 若要将 Fiddler 证书添加到你的设备，请转到**工具** > **Fiddler 选项** > **HTTPS**  >  **操作** > **导出到桌面的根证书**来获得 Fiddler 的证书。

5. 关闭捕获，以便可以以启动新的跟踪清除浏览器的缓存。

6. 1. 转到**文件** > **捕获流量**或按**F12**。
   2. 清除浏览器缓存，这样，所有缓存的项将删除，并且必须重新下载。

7. 创建请求： 

8. 1. 在编辑器选项卡上单击
   2. 设置 Http 方法和 URL
   3. 添加标头和请求正文，如果需要
   4. 单击执行

9. 开始再次捕获流量并完成页面上有问题的事务。

10. 完成后，请转到**文件** > **保存** > **所有会话**。

Fiddler 的详细信息[此处](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>后续步骤

如需查找问题的解决方案的更多帮助，下面是可以尝试的一些其他资源。

*  [博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



