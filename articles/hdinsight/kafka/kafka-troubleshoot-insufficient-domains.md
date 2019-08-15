---
title: 群集创建失败, 因为 Azure HDInsight 中的区域中的容错域不足
description: 群集创建失败, 因为 Azure HDInsight 中的区域中的容错域不足
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/09/2019
ms.openlocfilehash: 945bd6aff5bba96f8b72570442d3b1dcd035cb62
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947703"
---
# <a name="scenario-cluster-creation-failed-due-to-not-sufficient-fault-domains-in-region-in-azure-hdinsight"></a>方案:群集创建由于 Azure HDInsight `not sufficient fault domains in region`中的原因而失败

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

尝试创建 Apache Kafka 群集时`not sufficient fault domains in region`收到类似于的错误消息。

## <a name="cause"></a>原因

容错域是 Azure 数据中心基础硬件的逻辑分组。 每个容错域共享公用电源和网络交换机。 在 HDInsight 群集中实现节点的虚拟机和托管磁盘跨这些容错域分布。 此体系结构可限制物理硬件故障造成的潜在影响。

每个 Azure 区域都有特定数量的容错域。 有关域列表以及它们所包含的容错域的数目, 请参阅[可用性集](../../virtual-machines/windows/manage-availability.md)文档。

在 HDInsight 中, 需要在至少具有三个容错域的区域中预配 Kafka 群集。

## <a name="resolution"></a>解决

如果要创建群集的区域没有足够的容错域, 请与产品团队联系, 以允许设置群集, 即使没有三个容错域。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
