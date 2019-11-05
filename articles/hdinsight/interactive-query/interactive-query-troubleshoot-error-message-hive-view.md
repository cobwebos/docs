---
title: Apache Hive 视图中未显示的错误消息-Azure HDInsight
description: 查询在 Apache Hive 视图中失败，无需任何有关 Azure HDInsight 群集的详细信息。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 38dd064fe8365e6661ce7ea7e1077a4e1e32dbf5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494259"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>方案： Azure HDInsight Apache Hive 视图中未显示查询错误消息

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Apache Hive 视图查询错误消息将如下所示，没有进一步的信息：

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

有时，查询失败的错误消息可能太大，无法在 Hive 视图主页上显示。

## <a name="resolution"></a>分辨率

检查 Hive_view 右上角的“通知”选项卡，以查看完整的 Stacktrace 和错误消息。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 对订阅管理和付费支持的访问包含在你的 Microsoft Azure 订阅中，而技术支持通过其中一个 [Azure 支持计划](https://azure.microsoft.com/support/plans/)进行提供。
