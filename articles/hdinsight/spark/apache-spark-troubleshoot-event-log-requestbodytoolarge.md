---
title: Apache Spark 应用中出现 RequestBodyTooLarge 错误-Azure HDInsight
description: NativeAzureFileSystem ...在 Azure HDInsight 中 Apache Spark 流式处理应用的日志中显示 RequestBodyTooLarge
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894399"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem...RequestBodyTooLarge "在 HDInsight 中 Apache Spark 流式处理应用日志中显示

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

错误： `NativeAzureFileSystem ... RequestBodyTooLarge` 出现在 Apache Spark 流式处理应用程序的驱动程序日志中。

## <a name="cause"></a>原因

Spark 事件日志文件可能达到 WASB 的文件长度限制。

在 Spark 2.3 中，每个 Spark 应用生成一个 Spark 事件日志文件。 当应用程序运行时，Spark 流式处理应用程序的 Spark 事件日志文件继续增长。 目前，WASB 上的文件具有50000块限制，默认块大小为 4 MB。 因此在默认配置中，最大文件大小为 195 GB。 但是，Azure 存储已将最大块大小增加到 100 MB，这会将单个文件限制到 4.75 TB。 有关详细信息，请参阅[Blob 存储的可伸缩性和性能目标](../../storage/blobs/scalability-targets.md)。

## <a name="resolution"></a>分辨率

有三种可用于此错误的解决方案：

* 将块大小增加到最大 100 MB。 在 Ambari UI 中，修改 HDFS 配置属性 `fs.azure.write.request.size` （或在 `Custom core-site` 部分中创建它）。 将属性设置为更大的值，例如：33554432。 保存更新的配置并重新启动受影响的组件。

* 定期停止并重新提交 spark 流式处理作业。

* 使用 HDFS 存储 Spark 事件日志。 使用 HDFS 进行存储可能会导致群集缩放或 Azure 升级期间发生 Spark 事件数据丢失。

    1. 通过 Ambari UI 对 `spark.eventlog.dir` 和 `spark.history.fs.logDirectory` 进行更改：

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. 在 HDFS 上创建目录：

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. 通过 Ambari UI 重新启动所有受影响的服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
