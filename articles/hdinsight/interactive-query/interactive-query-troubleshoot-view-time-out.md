---
title: 在 Azure HDInsight 中提取查询结果时 Apache Hive 查看超时
description: 在 Azure HDInsight 中提取查询结果时 Apache Hive 查看超时
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 3e61103fceb668a9c8be381d1c4cc13efdc421f9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091272"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>场景：在 Azure HDInsight 中提取查询结果时 Apache Hive 查看超时

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

从 Apache Hive 视图运行某些查询时，可能会遇到以下错误：

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>原因

Hive 视图默认超时值可能不适用于正在运行的查询。 指定的时间段对于 Hive 视图而言太短，无法提取查询结果。

## <a name="resolution"></a>分辨率

通过在中`/etc/ambari-server/conf/ambari.properties`设置以下属性来增加 Apache Ambari Hive 视图超时。

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

在 Hive 视图`HIVE_VIEW_INSTANCE_NAME` URL 的末尾提供的值。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
