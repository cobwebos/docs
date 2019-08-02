---
title: Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 异常
description: Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 异常
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620982"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>方案:Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 异常

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache Spark 应用程序失败, 并出现 OutOfMemoryError 未经处理的异常。 你可能会收到类似于以下内容的错误消息:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

## <a name="cause"></a>原因

此异常的最可能原因是没有足够的堆内存。 当 Spark 应用程序作为执行程序或驱动程序运行时, 需要有足够的 Java 虚拟机 (JVM) 堆内存。

## <a name="resolution"></a>解决

1. 确定 Spark 应用程序将要处理的数据大小上限。 根据输入数据的最大大小、转换输入数据时生成的中间数据, 以及进一步转换中间数据时生成的输出数据, 对大小进行估计。 如果初始估计值不足, 请略微增加大小, 然后循环访问直到内存错误下降。

1. 请确保要使用的 HDInsight 群集具有足够的内存和核心资源，以便能够适应 Spark 应用程序。 若要确定资源是否足够，可以在群集的 YARN UI 的“群集指标”部分中查看“已用内存与内存总计”以及“已用 VCore 与 VCore 总计”的值。

    ![yarn 核心内存视图](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. 将以下 Spark 配置设置为合适的值。 将应用程序要求与群集中的可用资源进行平衡。 这些值不应超过 YARN 查看的可用内存和核心数 90%, 并且还应满足 Spark 应用程序的最低内存要求:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    所有执行程序使用的内存总量 =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    驱动程序使用的内存总量 =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* [Spark 内存管理概述](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [调试 HDInsight 群集上的 Spark 应用程序](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)。

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
