---
title: 尝试在 Azure HDInsight 中打开 Apache Spark 的历史记录服务器时出现 Java 堆空间错误
description: Apache Livy 服务器无法在 Azure HDInsight 中通过 OutOfMemoryError 启动
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667802"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>方案:尝试在 Azure HDInsight 中打开 Apache Spark 的历史记录服务器时出现 Java 堆空间错误

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

在 Spark 历史记录服务器中打开事件时, 会收到以下错误:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>原因

此问题通常是由于打开大型 spark 事件文件时资源不足引起的。 默认情况下, Spark 堆大小设置为 1 GB, 但较大的 Spark 事件文件可能需要超过此大小。

如果你想要验证尝试加载的文件的大小, 则可以执行以下命令:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>解决

可以通过在 spark 配置中编辑`SPARK_DAEMON_MEMORY`属性并重新启动所有服务来增加 spark 历史记录服务器内存。

可以通过选择 Custom-spark2-defaults/Config/Advanced custom-spark2-defaults 节, 在 Ambari 浏览器 UI 中执行此操作。

![Advanced custom-spark2-defaults-env 部分](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

添加以下属性, 将 Spark 历史记录服务器内存从1g 更改为 4g: `SPARK_DAEMON_MEMORY=4g`。

![Spark 属性](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

请确保从 Ambari 重新启动所有受影响的服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
