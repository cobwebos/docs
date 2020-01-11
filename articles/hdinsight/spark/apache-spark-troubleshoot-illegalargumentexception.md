---
title: Apache Spark 的 IllegalArgumentException 错误-Azure HDInsight
description: Azure HDInsight for Azure 数据工厂中 Apache Spark 活动的 IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894385"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>方案： Azure HDInsight 中 Apache Spark 活动的 IllegalArgumentException

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

尝试在 Azure 数据工厂管道中执行 Spark 活动时，会收到以下异常：

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>原因

如果应用程序 jar 文件不在 Spark 群集的默认/主存储中，则 Spark 作业将失败。

这是在此 bug 中跟踪的 Spark 开源框架的已知问题：[如果 fs.defaultfs 和应用程序 jar 不同 url，spark 作业将失败](https://issues.apache.org/jira/browse/SPARK-22587)。

Spark 2.3.0 中已解决此问题。

## <a name="resolution"></a>分辨率

请确保将应用程序 jar 存储在 HDInsight 群集的默认/主存储上。 对于 Azure 数据工厂，请确保 ADF 链接服务指向 HDInsight 默认容器，而不是辅助容器。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
