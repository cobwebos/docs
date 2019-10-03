---
title: Apache ZooKeeper server 无法在 Azure HDInsight 中形成仲裁
description: Apache ZooKeeper server 无法在 Azure HDInsight 中形成仲裁
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 7091e638743fb8cd1488fe7e332378bf89304af1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087071"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper server 无法在 Azure HDInsight 中形成仲裁

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache ZooKeeper 服务器运行不正常，则可能出现以下症状：资源管理器/名称节点处于备用模式，简单 HDFS 操作不起`zkFailoverController`作用，已停止且无法启动，Yarn/Spark/Livy 作业因 ZooKeeper 错误而失败。 你可能会看到类似于以下内容的错误消息：

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

## <a name="cause"></a>原因

如果快照文件的量很大或快照文件已损坏，ZooKeeper server 将无法形成仲裁，这将导致 ZooKeeper 相关服务运行不正常。 ZooKeeper 服务器不会从其数据目录中删除旧的快照文件，而是由用户定期执行任务来维护 ZooKeeper 的正常。 有关详细信息，请参阅[ZooKeeper 的优势和限制](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)。

## <a name="resolution"></a>分辨率

检查 ZooKeeper 数据目录`/hadoop/zookeeper/version-2` ， `/hadoop/hdinsight-zookeepe/version-2`查明快照文件的大小是否大。 如果存在较大的快照，请执行以下步骤：

1. 备份和`/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeepe/version-2`中的快照。

1. 清理和`/hadoop/zookeeper/version-2` `/hadoop/hdinsight-zookeepe/version-2`中的快照。

1. 从 Apache Ambari UI 重启所有 ZooKeeper 服务器。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

- [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

- 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
