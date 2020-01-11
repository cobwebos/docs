---
title: 当 Azure HDInsight 存储包含多个文件时，Apache Spark 慢速
description: 当 Azure 存储容器在 Azure HDInsight 中包含多个文件时，Apache Spark 作业运行缓慢
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894329"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>当 Azure 存储容器在 Azure HDInsight 中包含许多文件时，Apache Spark 作业运行缓慢

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

运行 HDInsight 群集时，当有多个文件/子文件夹时，写入 Azure 存储容器的 Apache Spark 作业将变慢。 例如，写入到新容器时需要20秒，但写入包含功能文件的容器大约需要2分钟。

## <a name="cause"></a>原因

这是一个已知的 Spark 问题。 在 Spark 作业执行期间，缓慢来自 `ListBlob` 和 `GetBlobProperties` 操作。

为了跟踪分区，Spark 必须维护一个 `FileStatusCache`，其中包含有关目录结构的信息。 使用此缓存，Spark 可以分析路径并识别可用分区。 跟踪分区的好处是，Spark 只在读取数据时才触及必要的文件。 若要将此信息保持为最新状态，则在写入新数据时，Spark 必须列出该目录下的所有文件，并更新该缓存。

在 Spark 2.1 中，虽然我们不需要在每次写入后更新缓存，但 Spark 会检查现有分区列是否与当前写入请求中的建议的分区列匹配，因此它还会在每次写入时导致列出操作。

在 Spark 2.2 中，当用追加模式写入数据时，应修复此性能问题。

## <a name="resolution"></a>分辨率

创建分区数据集时，请务必使用分区方案，该方案将限制 Spark 必须列出以更新 `FileStatusCache`的文件数。

对于每个第 n 个微批处理，其中 N %100 = = 0 （100只是一个示例），请将现有数据移到另一个目录，该目录可由 Spark 加载。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
