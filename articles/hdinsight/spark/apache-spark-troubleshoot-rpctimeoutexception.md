---
title: Azure HDInsight 中 Apache Spark thrift 服务器的 RpcTimeoutException
description: 使用 Apache Spark thrift 服务器处理大型数据集时，会看到502错误
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 220465105733b715c1990fd73904b9dc095637d4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088597"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>场景：Azure HDInsight 中 Apache Spark thrift 服务器的 RpcTimeoutException

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Spark 应用程序失败`org.apache.spark.rpc.RpcTimeoutException`并出现异常和消息： `Futures timed out`，如以下示例中所示：

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`和`overhead limit exceeded`错误也可能出现`sparkthriftdriver.log`在中，如以下示例中所示：

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>原因

这些错误是由于在数据处理过程中内存资源不足引起的。 如果启动 Java 垃圾回收过程，可能会导致 Spark 应用程序挂起。 查询将开始超时并停止处理。 此`Futures timed out`错误表示群集出现严重压力。

## <a name="resolution"></a>分辨率

增加群集大小，方法是添加更多的工作节点或增加现有群集节点的内存容量。 您还可以调整数据管道以减少一次处理的数据量。

`spark.network.timeout`控制所有网络连接的超时。 增加网络超时可能需要更多的时间来完成一些关键操作，但这不会完全解决问题。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
