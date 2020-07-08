---
title: 了解并解决 HDInsight 上的 WebHCat 错误 - Azure
description: 了解 HDInsight 上的 WebHCat 返回的常见错误以及如何解决它们。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 021bfc0b87b0da800728eda26d9f5222bd52bc1e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086953"
---
# <a name="understand-and-resolve-errors-received-from-webhcat-on-hdinsight"></a>了解和解决从 HDInsight 上的 WebHCat 收到的错误

了解将 WebHCat 用于 HDInsight 时接收的错误以及如何解决错误。 WebHCat 由 Azure PowerShell 和 Data Lake Tools for Visual Studio 等客户端工具在内部使用。

## <a name="what-is-webhcat"></a>什么是 WebHCat

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) 是适用于 [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog) 的 REST API，是针对 Apache Hadoop 的表和存储管理层。 WebHCat 默认情况下在 HDInsight 群集上处于启用状态，可供各种工具在执行提交作业、获取作业状态等操作时使用，无需登录到群集中。

## <a name="modifying-configuration"></a>修改配置

本文档中列出的几大错误之所以发生，是因为超出了配置的最大值。 当解决步骤提到可以更改某个值时，请使用 Apache Ambari（Web 或 REST API）修改该值。 有关详细信息，请参阅[使用 Apache Ambari 管理 HDInsight](hdinsight-hadoop-manage-ambari.md)

### <a name="default-configuration"></a>默认配置

如果超过以下默认值，则可能降低 WebHCat 性能或导致错误：

| 设置 | 作用 | 默认值 |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |可以同时处于活动状态（挂起或运行）的最大作业数 |10,000 |
| [templeton.exec.max-procs][max-procs] |可以同时处理的最大请求数 |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |作业历史记录保留的天数 |7 天 |

## <a name="too-many-requests"></a>请求过多

**HTTP 状态代码**：429

| 原因 | 解决方法 |
| --- | --- |
| 已超过 WebHCat 每分钟能够处理的最大并发请求数（默认值为 20） |减少工作负荷以确保提交的数量没有超出最大并发请求数，或者通过修改 `templeton.exec.max-procs` 来提高并发请求限制。 有关详细信息，请参阅[修改配置](#modifying-configuration) |

## <a name="server-unavailable"></a>服务器不可用

**HTTP 状态代码**：503

| 原因 | 解决方法 |
| --- | --- |
| 此状态代码通常发生在群集的主要和辅助 HeadNode 之间进行故障转移时 |等待两分钟，并重试该操作 |

## <a name="bad-request-content-could-not-find-job"></a>请求内容错误：找不到作业

**HTTP 状态代码**：400

| 原因 | 解决方法 |
| --- | --- |
| 作业详细信息已被作业历史记录清除器清除 |作业历史记录的默认保留期为 7 天。 通过修改 `mapreduce.jobhistory.max-age-ms` 可更改默认保留期。 有关详细信息，请参阅[修改配置](#modifying-configuration) |
| 作业由于故障转移而终止 |重试提交作业，重试时间最多两分钟 |
| 使用了无效的作业 ID |检查作业 ID 是否正确 |

## <a name="bad-gateway"></a>网关错误

**HTTP 状态代码**：502

| 原因 | 解决方法 |
| --- | --- |
| WebHCat 进程内发生内部垃圾回收 |等待垃圾回收完成或重新启动 WebHCat 服务 |
| 等待 ResourceManager 服务的响应超时。 当活动应用程序的数量达到配置的最大值（默认为 10,000）时，可能会发生此错误 |等待当前正在运行的作业完成，或者通过修改 `yarn.scheduler.capacity.maximum-applications` 来提高并发作业限制。 有关详细信息，请参阅[修改配置](#modifying-configuration)部分。 |
| 在 `Fields` 设置为 `*` 时，尝试通过 [GET /jobs ](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) 调用来检索所有作业 |不要检索全部作业详细信息。 而是改用 `jobid` 来仅检索作业 ID 大于特定作业 ID 的作业的详细信息。 或者，不要使用 `Fields` |
| 在 HeadNode 故障转移期间 WebHCat 服务关闭 |等待两分钟，并重试该操作 |
| 通过 WebHCat 提交的作业有超过 500 个处于挂起状态 |等到当前挂起的作业完成再提交更多作业 |

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心 。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，技术支持将通过某个 [Azure 支持计划](https://azure.microsoft.com/support/plans/)提供。

[maximum-applications]: https://docs.cloudera.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://cwiki.apache.org/confluence/display/Hive/WebHCat+Configure#WebHCatConfigure-WebHCatConfiguration
[max-age-ms]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
