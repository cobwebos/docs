---
title: RpcTimeoutException for Apache Spark thrift-Azure HDInsight
description: 使用 Apache Spark thrift 服务器处理大型数据集时看到 502 错误
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894285"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Spark thrift 服务器发生 RpcTimeoutException

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Spark 应用程序失败并出现 `org.apache.spark.rpc.RpcTimeoutException` 异常和以下示例中所示的消息：`Futures timed out`：

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`sparkthriftdriver.log` 中还可能出现 `OutOfMemoryError` 和 `overhead limit exceeded` 错误，如以下示例中所示：

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>原因

这些错误的原因是数据处理期间内存资源不足。 如果启动 Java 垃圾回收进程，可能会导致 Spark 应用程序挂起。 查询将开始超时并停止处理。 `Futures timed out` 错误表示群集遭受严重的压力。

## <a name="resolution"></a>解决方法

通过添加更多的工作器节点或增加现有群集节点的内存容量，来增大群集大小。 还可以调整数据管道，以减少一次性处理的数据量。

`spark.network.timeout` 将控制所有网络连接的超时。 增大网络超时可为某些关键操作提供更多的时间，但不能彻底解决该问题。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
