---
title: Azure HDInsight 中的阿帕奇安巴里使用情况
description: 讨论如何使用 Apache Ambari 在 Azure HDInsight 中。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77067391"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight 中的阿帕奇安巴里使用情况

HDInsight 使用 Apache Ambari 进行群集部署和管理。 Ambari 代理在每个节点上运行（头节点、辅助节点、动物园管理员和边缘节点（如果存在）。 Ambari 服务器仅在头节点上运行（hn0 或 hn1）。 一次只能运行一个 Ambari 服务器实例。 这是由 HDInsight 故障转移控制器控制的。 当其中一个头节点关闭以进行重新启动或维护时，另一个头节点将变为活动状态，第二个头节点上的 Ambari 服务器将启动。

所有群集配置都应通过[Ambari UI](./hdinsight-hadoop-manage-ambari.md)完成，任何本地更改都将在重新启动节点时被覆盖。

## <a name="failover-controller-services"></a>故障转移控制器服务

HDInsight 故障转移控制器还负责更新头节点主机的 IP 地址，该地址指向当前活动头节点。 所有 Ambari 代理都配置为向头节点主机报告其状态和检测信号。 故障转移控制器是在群集中的每个节点上运行的服务集，如果它们未运行，则头节点故障转移可能无法正常工作，在尝试访问 Ambari 服务器时，您将获得 HTTP 502。

要检查哪个头节点处于活动状态，一种方法是 ssh 到群集中的一个节点，然后运行`ping headnodehost`IP 并将其与两个头节点的 IP 进行比较。

如果故障转移控制器服务未运行，头节点故障转移可能无法正确发生，最终可能无法运行 Ambari 服务器。 要检查故障转移控制器服务是否正在运行，请执行：

```bash
ps -ef | grep failover
```

## <a name="logs"></a>日志

在活动头节点上，您可以在以下处检查 Ambari 服务器日志：

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

在群集中的任何节点上，都可以在以下处检查 Ambari 代理日志：

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>服务启动序列

这是启动时开始的服务序列：

1. Hdinsight 代理启动故障转移控制器服务。
1. 故障转移控制器服务在每个节点上启动 Ambari 代理，在活动头节点上启动 Ambari 服务器。

## <a name="ambari-database"></a>安巴里数据库

HDInsight 在引擎盖下创建 SQL Azure 数据库，作为 Ambari 服务器的数据库。 默认[服务层为 S0](../sql-database/sql-database-elastic-pool-scale.md)。

对于创建群集时，辅助节点计数大于 16 的任何群集，S2 是数据库服务层。

## <a name="takeaway-points"></a>外卖点

切勿手动启动/停止 ambari 服务器或 ambari 代理服务，除非您尝试重新启动服务以解决问题。 要强制故障转移，可以重新启动活动头节点。

切勿手动修改任何群集节点上的任何配置文件，让 Ambari UI 为您完成工作。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)
* [使用 Apache Ambari REST API 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari-rest-api.md)

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 用于改善客户体验的官方 Microsoft Azure 帐户连接。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
