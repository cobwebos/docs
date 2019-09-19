---
title: 群集节点在 Azure HDInsight 中的磁盘空间不足
description: 排查 Azure HDInsight 中 Apache Hadoop 群集节点磁盘空间问题。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: a9d82e0465e555d4959e549e04565399d423c1ee
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087360"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>场景：群集节点在 Azure HDInsight 中的磁盘空间不足

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

作业可能失败并出现类似于以下的错误消息：`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

否则，可能会收到类似于以下内容的`local-dirs usable space is below configured utilization percentage`Apache Ambari 警报：。

## <a name="cause"></a>原因

Apache Yarn 应用程序缓存可能已使用了所有可用磁盘空间。 Spark 应用程序可能运行效率低下。

## <a name="resolution"></a>分辨率

1. 使用 Ambari UI 确定哪个节点的磁盘空间不足。

1. 确定麻烦节点中的哪个文件夹对大部分磁盘空间构成。 首先，通过 SSH 连接到节点， `df`然后运行以列出所有装载的磁盘使用情况。 通常，它`/mnt`是 OSS 使用的临时磁盘。 你可以在文件夹中输入，然后键入`sudo du -hs`以显示文件夹下的摘要文件大小。 如果看到类似`/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`于的文件夹，则表示应用程序仍在运行。 这可能是由于 RDD 暂留或中间无序文件引起的。

1. 若要缓解此问题，请终止应用程序，这将释放该应用程序使用的磁盘空间。

1. 若要最终解决该问题，请优化应用程序。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
