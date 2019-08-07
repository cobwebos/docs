---
title: Azure HDInsight Apache Hive 视图中未显示查询错误消息
description: 在 Hive 视图中查询失败, 没有任何详细信息
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: e87bb42b09135b61dd4cf32c9ae30b0113d800dc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829029"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>方案:Azure HDInsight Apache Hive 视图中未显示查询错误消息

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache Hive 查看查询错误消息将如下所示, 无需进一步的信息:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

有时, 查询失败的错误消息可能太大, 无法在 Hive 视图主页上显示。

## <a name="resolution"></a>解决

检查 Hive_view 右上角的 "通知" 选项卡, 以查看完整的 Stacktrace 和错误消息。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
