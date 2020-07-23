---
title: 来自 Apache Spark 应用的 RequestBodyTooLarge 错误 - Azure HDInsight
description: NativeAzureFileSystem ...RequestBodyTooLarge 显示在 Azure HDInsight 上 Apache Spark 流应用的日志中
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894399"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>“NativeAzureFileSystem...RequestBodyTooLarge”出现在 HDInsight 的 Apache Spark 流式处理应用日志中

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

错误 `NativeAzureFileSystem ... RequestBodyTooLarge` 显示在 Apache Spark 流应用的驱动程序日志中。

## <a name="cause"></a>原因

Spark 事件日志文件可能达到了 WASB 的文件长度限制。

在 Spark 2.3 中，每个 Spark 应用会生成一个 Spark 事件日志文件。 在 Spark 流应用运行过程中，其事件日志文件会不断增大。 目前，WASB 上的文件的块大小限制为 50000，默认块大小为 4 MB。 因此在默认配置中，最大文件大小为 195 GB。 但是，Azure 存储已将最大块大小增加到 100 MB，这会将单个文件限制到 4.75 TB。 有关详细信息，请参阅 [Blob 存储可伸缩性和性能目标](../../storage/blobs/scalability-targets.md)。

## <a name="resolution"></a>解决方法

对于此错误，可以采用三种解决方法：

* 将块大小增大至最大 100 MB。 在 Ambari UI 中，修改 HDFS 配置属性 `fs.azure.write.request.size`（或者在 `Custom core-site` 节中创建该配置）。 将该属性设置为更大的值，例如：33554432。 保存更新的配置并重启受影响的组件。

* 定期停止并重新提交 Spark 流作业。

* 使用 HDFS 来存储 Spark 事件日志。 使用 HDFS 存储日志可能会在群集缩放或 Azure 升级期间导致 Spark 事件数据丢失。

    1. 通过 Ambari UI 对 `spark.eventlog.dir` 和 `spark.history.fs.logDirectory` 做出更改：

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

    1. 通过 Ambari UI 重启所有受影响的服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区与适当的资源（解答、支持人员和专家）相关联来改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
