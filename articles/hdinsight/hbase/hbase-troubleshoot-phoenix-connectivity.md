---
title: Azure HDInsight 中的 Apache Phoenix 连接问题
description: Apache HBase 和 Azure HDInsight 中 Apache Phoenix 之间的连接问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887285"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Phoenix 连接问题

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

无法使用 Apache Phoenix 连接到 Apache HBase。 原因可能各不相同。

## <a name="cause-incorrect-ip"></a>原因：IP 不正确

活动 Zookeeper 节点的 IP 不正确。

### <a name="resolution"></a>解决方法

可以通过打开指向“HBase”   > “快速链接”   > “ZK(活动)”   > “ZooKeeper 信息”  的链接，从 Ambari UI 确定活动 ZooKeeper 节点的 IP。 根据需要更正 IP。

---

## <a name="cause-systemcatalog-table-offline"></a>原因：SYSTEM.CATALOG 表脱机

运行 `!tables` 等命令时，会收到类似于以下内容的错误消息：

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

运行 `count 'SYSTEM.CATALOG'` 等命令时，会收到类似于以下内容的错误消息：

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>解决方法

从 Apache Ambari UI 完成以下步骤，以在所有 ZooKeeper 节点上重启 HMaster 服务：

1. 从 HBase 的“摘要”部分，转到“HBase” > “Active HBase Master”。   

1. 从“组件”部分重启 HBase Master 服务。 

1. 为所有剩余的“Standby HBase Master”服务重复以上步骤。 

HBase Master 服务最多可能需要五分钟才能稳定下来并完成恢复。 在 `SYSTEM.CATALOG` 表恢复正常后，与 Apache Phoenix 的连接问题应该会自动得到解决。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
