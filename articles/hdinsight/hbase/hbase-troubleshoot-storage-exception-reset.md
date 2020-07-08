---
title: 连接重置后 Azure HDInsight 中出现存储异常
description: 连接重置后 Azure HDInsight 中出现存储异常
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887217"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>方案：连接重置后 Azure HDInsight 中出现存储异常

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

无法创建新的 Apache HBase 表。

## <a name="cause"></a>原因

在表截断过程中出现了存储连接问题。 已在 HBase 元数据表中删除表项。 只保留了一个 Blob 文件，并删除了所有其他文件。

存储中没有名为 `/hbase/data/default/ThatTable` 的文件夹 Blob。 WASB 驱动程序发现存在上述 Blob 文件，因此不允许创建名为 `/hbase/data/default/ThatTable` 的任何 Blob（因为它假设父文件夹已存在），因此创建表将会失败。

## <a name="resolution"></a>解决方法

1. 从 Apache Ambari UI 重启活动的 HMaster。 这样，两个备用 HMaster 中的一个将成为活动的 HMaster，而新的活动 HMaster 将重新加载元数据表信息。 因此，在 HMaster UI 中不会看到 `already-deleted` 表。

1. 可以通过 Cloud Explorer 等 UI 工具，或运行类似于 `hdfs dfs -ls /xxxxxx/yyyyy` 的命令来查找孤立的 Blob 文件。 运行 `hdfs dfs -rmr /xxxxx/yyyy` 可删除该 Blob。 例如，`hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`。

现在，可以在 HBase 中创建同名的新表。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
