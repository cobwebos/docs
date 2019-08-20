---
title: Azure HDInsight 中的 Apache Phoenix 连接问题
description: Azure HDInsight 中的 Apache Phoenix 连接问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/14/2019
ms.openlocfilehash: 66077416dca4048fc99047f7d6b967e55aab0a23
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575736"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>场景：Azure HDInsight 中的 Apache Phoenix 连接问题

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

无法连接到带有 Apache Phoenix 的 Apache HBase。 原因可能有所不同。

## <a name="cause-incorrect-ip"></a>原因：IP 不正确

活动 Zookeeper 节点的 IP 不正确。

### <a name="resolution"></a>分辨率

可以通过遵循**HBase** > **Quick links** > **ZK (active)**  > **Zookeeper 信息**的链接, 在 Ambari UI 中标识活动 Zookeeper 节点的 IP。 根据需要更正 IP。

---

## <a name="cause-systemcatalog-table-offline"></a>原因：主板.脱机目录表

运行之类`!tables`的命令时, 会收到类似于以下内容的错误消息:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

运行之类`count 'SYSTEM.CATALOG'`的命令时, 会收到类似于以下内容的错误消息:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>分辨率

在 Apache Ambari UI 中完成以下步骤, 在所有 ZooKeeper 节点上重新启动 HMaster 服务:

1. 从 HBase 的**摘要**部分, 中转到**hbase** > **Active HBase Master**。

1. 从 "**组件**" 部分, 重新启动 HBase Master 服务。

1. 为所有剩余的“Standby HBase Master”服务重复以上步骤。

HBase Master 服务可能需要长达五分钟的时间才能稳定并完成恢复。 `SYSTEM.CATALOG`表回到正常状态后, Apache Phoenix 的连接问题应自动得到解决。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
