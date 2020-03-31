---
title: Apache ZooKeeper 服务器无法在 Azure HDInsight 中形成仲裁
description: Apache ZooKeeper 服务器无法在 Azure HDInsight 中形成仲裁
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: 4e46efaf17ae9bad5df6f1f61f401d3e6de58a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250241"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper 服务器无法在 Azure HDInsight 中形成仲裁

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Apache ZooKeeper 服务器运行不正常，症状可能包括：资源管理器/名称节点处于备用模式、简单的 HDFS 操作不起作用、`zkFailoverController` 处于停止状态且无法启动、Yarn/Spark/Livy 作业因 Zookeeper 错误而失败。 LLAP 守护进程也可能无法在安全火花或交互式蜂巢群集上启动。 可能会看到如下所示的错误消息：

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

在动物园管理员服务器日志上任何动物园管理员主机在 /var/log/zookeeper/zookeeper-zookeeper-server-out\*中，您可能还会看到以下错误：

```
2020-02-12 00:31:52,513 - ERROR [CommitProcessor:1:NIOServerCnxn@178] - Unexpected Exception:
java.nio.channels.CancelledKeyException
```

## <a name="cause"></a>原因

当快照文件的卷较大或快照文件损坏时，ZooKeeper 服务器将无法形成仲裁，导致 ZooKeeper 相关服务运行不正常。 ZooKeeper 服务器不会从其数据目录中删除旧的快照文件，而只能由用户定期维护 ZooKeeper 的正常状况。 有关详细信息，请参阅 [ZooKeeper 的优势和限制](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)。

## <a name="resolution"></a>解决方法

检查 ZooKeeper 数据目录 `/hadoop/zookeeper/version-2` 和 `/hadoop/hdinsight-zookeeper/version-2`，确定快照文件大小是否过大。 如果存在大的快照，请执行以下步骤：

1. 在 `/hadoop/zookeeper/version-2` 和 `/hadoop/hdinsight-zookeeper/version-2` 中备份快照。

1. 在 `/hadoop/zookeeper/version-2` 和 `/hadoop/hdinsight-zookeeper/version-2` 中清理快照。

1. 在 Apache Ambari UI 中重启所有 ZooKeeper 服务器。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

- 与[@AzureSupport](https://twitter.com/azuresupport)- 用于改善客户体验的官方 Microsoft Azure 帐户连接。 将 Azure 社区连接到正确的资源：答案、支持和专家。

- 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
