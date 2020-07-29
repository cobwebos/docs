---
title: Azure HDInsight 中的化简器速度缓慢
description: 可能的数据倾斜导致 Azure HDInsight 中的化简器速度缓慢
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895164"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>方案：Azure HDInsight 中的化简器速度缓慢

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

运行某个查询（例如 `insert into table1 partition(a,b) select a,b,c from table2`）时，查询计划将启动许多化简器，但每个分区中的数据将进入单个化简器。 这会导致查询花费的时间与最大分区的化简器所花费的时间相当。

## <a name="cause"></a>原因

打开 [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) 并检查集 `hive.optimize.sort.dynamic.partition` 的值。

应该根据数据的性质将此变量的值设置为 true/false。

如果输入表中的分区较少（假设少于 10 个），并且输出分区的数目也较少，如果将该变量设置为 `true`，则会导致使用每个分区的单个化简器对数据进行全局排序和写入。 即使可用化简器的数目较多，也可能会由于数据倾斜而导致少量的化简器出现滞后，并且无法获得最大并行度。 更改为 `false` 后，多个化简器可以处理单个分区，并可写出多个较小文件，因而加快插入速度。 不过，由于存在较小文件，这可能会影响后续的查询。

如果分区数较多并且数据未倾斜，则最好是使用 `true` 值。 在这种情况下，将会写出映射阶段的结果，使每个分区由单个化简器处理，从而提高后续查询的性能。

## <a name="resolution"></a>解决方法

1. 尝试将要规范化的数据重新分区成多个分区。

1. 如果方法 #1 不可行，请在 beeline 会话中将配置值设置为 false，然后重试查询。 `set hive.optimize.sort.dynamic.partition=false`。 不建议在群集级别将值设置为 false。 `true` 值是最佳的；请根据数据和查询的性质按需设置参数。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区与适当的资源（解答、支持人员和专家）相关联来改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
