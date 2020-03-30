---
title: Apache Spark 的非法参数异常错误 - Azure HDInsight
description: Azure 数据工厂的 Azure HDInsight 中 Apache Spark 活动的 IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894385"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>方案：Azure HDInsight 中 Apache Spark 活动的非法参数例外

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

尝试在 Azure 数据工厂管道中执行 Spark 活动时，收到以下异常：

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>原因

如果应用程序 jar 文件不在 Spark 群集的默认/主存储中，则 Spark 作业将失败。

这是一个已知的问题与在这个Bug中跟踪的Spark开源框架：[如果fs.defaultFS和应用程序jar是不同的URL，Spark作业将失败](https://issues.apache.org/jira/browse/SPARK-22587)。

此问题已在 Spark 2.3.0 中解决。

## <a name="resolution"></a>解决方法

请确保将应用程序 jar 存储在 HDInsight 群集的默认/主存储上。 对于 Azure 数据工厂，请确保 ADF 链接服务指向 HDInsight 默认容器，而不是辅助容器。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帐户连接 Azure 社区，以将 Azure 社区连接到正确的资源：答案、支持和专家，从而改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
