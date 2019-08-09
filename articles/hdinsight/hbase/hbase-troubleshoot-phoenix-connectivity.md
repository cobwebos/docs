---
title: Azure HDInsight 中的 Apache Phoenix 连接问题
description: Azure HDInsight 中的 Apache Phoenix 连接问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68887035"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>方案:Azure HDInsight 中的 Apache Phoenix 连接问题

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

无法连接到带有 Apache Phoenix 的 Apache HBase。 原因可能有所不同。

## <a name="cause-incorrect-ip"></a>原因：IP 不正确

活动 zookeeper 节点的 IP 不正确。

### <a name="resolution"></a>解决

可以通过遵循**HBase > 快速链接-> ZK***  **(活动)-> zookeeper 信息**的链接, 在 Ambari UI 中标识 active zookeeper 节点的 IP。 根据需要进行更正。

---

## <a name="cause-systemcatalog-table-offline"></a>原因：主板.脱机目录表

运行之类`!tables`的命令时, 会收到类似于以下内容的错误消息:

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

运行之类`count 'SYSTEM.CATALOG'`的命令时, 会收到类似于以下内容的错误消息:

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>解决

从 Ambari UI 中的所有 zookeeper 节点上重新启动 HMaster 服务。

1. 请参阅 HBase 的摘要部分中的**hbase-> Active HBase Master**链接。

1. 在 "**组件**" 部分中, 重新启动 HBase Master 服务。

1. 对于剩余的**备用 HBase Master**服务, 请重复上述步骤。

HBase Master 服务稳定并完成恢复, 可能需要长达5分钟的时间。 `SYSTEM.CATALOG`表回到正常状态后, Apache Phoenix 的连接问题应该会自动得到解决。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
