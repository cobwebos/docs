---
title: RpcTimeoutException for Apache Spark thrift-Azure HDInsight
description: 使用 Apache Spark thrift 服务器处理大型数据集时，会看到502错误
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894285"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>方案： Azure HDInsight 中 Apache Spark thrift 服务器的 RpcTimeoutException

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Spark 应用程序失败并出现 `org.apache.spark.rpc.RpcTimeoutException` 异常和消息： `Futures timed out`，如以下示例中所示：

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` 和 `overhead limit exceeded` 错误也可能出现在 `sparkthriftdriver.log` 中，如以下示例所示：

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>原因

这些错误是由于在数据处理过程中内存资源不足引起的。 如果启动 Java 垃圾回收过程，可能会导致 Spark 应用程序挂起。 查询将开始超时并停止处理。 `Futures timed out` 错误表明群集出现严重压力。

## <a name="resolution"></a>分辨率

增加群集大小，方法是添加更多的工作节点或增加现有群集节点的内存容量。 您还可以调整数据管道以减少一次处理的数据量。

`spark.network.timeout` 控制所有网络连接的超时。 增加网络超时可能需要更多的时间来完成一些关键操作，但这不会完全解决问题。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
