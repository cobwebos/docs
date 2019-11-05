---
title: HDP upgrade & Azure HDInsight Apache Phoenix 视图中没有数据
description: HDP 升级导致 Azure HDInsight 的 Apache Phoenix 视图中没有数据
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: 148c7ea4bb3eef3fff7c1a8fd3b865791613704f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498027"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>方案： HDP 升级导致 Azure HDInsight Apache Phoenix 视图中没有数据

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

从 HDP 2.4 升级到 HDP 2.5 后，Apache Phoenix 视图不包含日期。

## <a name="cause"></a>原因

视图的索引表（视图的所有索引都存储在单个物理 Apache HBase 表中）在升级过程中被截断

## <a name="resolution"></a>分辨率

升级后，删除并重新创建所有视图索引。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 对订阅管理和付费支持的访问包含在你的 Microsoft Azure 订阅中，而技术支持通过其中一个 [Azure 支持计划](https://azure.microsoft.com/support/plans/)进行提供。
