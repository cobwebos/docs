---
title: Azure HDInsight 中的化简器速度缓慢
description: 化简器在 Azure HDInsight 中的速度慢于可能的数据倾斜
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5fe1b2c720bbc55e8e245f9592755e046e0b04a3
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968431"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>方案:Azure HDInsight 中的化简器速度缓慢

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

当运行查询 ( `insert into table1 partition(a,b) select a,b,c from table2`如查询计划) 时, 将启动一组化简器, 但每个分区中的数据将进入单个化简器。 这会使查询速度慢于最大分区的化简器所花的时间。

## <a name="cause"></a>原因

打开[beeline](../hadoop/apache-hadoop-use-hive-beeline.md)并验证设置`hive.optimize.sort.dynamic.partition`的值。

此变量的值旨在根据数据的性质设置为 true/false。

如果输入表中的分区较少 (即小于 10), 并且是输出分区的数目, 并且该变量设置为`true`, 则这将导致数据使用每个分区的单个化简器进行全局排序和写入。 即使可用的化简器数量较大, 也可能由于数据歪斜而导致化简器的数目不高, 并且无法获得最大并行度。 更改为`false`时, 多个化简器可以处理单个分区, 并写出多个较小的文件, 从而加快插入速度。 由于存在较小的文件, 这可能会影响进一步的查询。

如果分区数量`true`较大, 并且数据不是偏差, 则值为有意义。 在这种情况下, 将编写 map 阶段的结果, 以便每个分区将由单个化简器处理, 从而提高后续的查询性能。

## <a name="resolution"></a>解决

1. 尝试对数据进行重新分区, 以规范化为多个分区。

1. 如果 #1 不可行, 请在 beeline 会话中将配置的值设置为 false, 然后重试查询。 `set hive.optimize.sort.dynamic.partition=false`。 不建议在群集级别将值设置为 false。 的值`true`是最佳的, 根据数据和查询的性质, 根据需要设置参数。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
