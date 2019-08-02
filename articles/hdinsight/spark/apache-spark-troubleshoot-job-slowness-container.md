---
title: 当 Azure 存储容器在 Azure HDInsight 中包含多个文件时, Apache Spark 作业运行缓慢
description: 当 Azure 存储容器在 Azure HDInsight 中包含多个文件时, Apache Spark 作业运行缓慢
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 78dff1b9d9db4e54ab1a8f7203088753e206c610
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641950"
---
# <a name="scenario-apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>方案:当 Azure 存储容器在 Azure HDInsight 中包含许多文件时, Apache Spark 作业运行缓慢

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

运行 HDInsight 群集时, 当有多个文件/子文件夹时, 写入 Azure 存储容器的 Apache Spark 作业将变慢。 例如, 写入到新容器时需要20秒, 但写入包含功能文件的容器大约需要2分钟。

## <a name="cause"></a>原因

这是一个已知的 Spark 问题。 在 Spark 作业执行期间`ListBlob` , `GetBlobProperties`缓慢来自和操作。

为了跟踪分区, Spark 必须维护`FileStatusCache` , 其中包含有关目录结构的信息。 使用此缓存, Spark 可以分析路径并识别可用分区。 跟踪分区的好处是, Spark 只在读取数据时才触及必要的文件。 若要将此信息保持为最新状态, 则在写入新数据时, Spark 必须列出该目录下的所有文件, 并更新该缓存。

在 Spark 1.6 中, 每次更新目录时, (1) 清除缓存 (2) 以递归方式列出所有文件, (3) 更新整个缓存。 这将导致多次列出操作。

在 Spark 2.1 中, 虽然我们不需要在每次写入后更新缓存, 但 Spark 会检查现有分区列是否与当前写入请求中的建议的分区列匹配, 因此它还会在每次写入时导致列出操作。

在 Spark 2.2 中, 当用追加模式写入数据时, 应修复此性能问题。

## <a name="resolution"></a>解决

创建分区的数据集时, 请务必使用分区方案, 该方案将限制 Spark 需要列出以更新的`FileStatusCache`文件数。

对于每个第 n 个微批处理, 其中 N% 100 = = 0 (100 只是一个示例), 请将现有数据移到另一个目录, 该目录可由 Spark 加载。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
