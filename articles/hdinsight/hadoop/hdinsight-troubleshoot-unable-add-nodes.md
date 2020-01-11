---
title: 无法将节点添加到 Azure HDInsight 群集
description: 排查无法在 Azure HDInsight 中将节点添加到 Apache Hadoop 群集的原因
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894099"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>方案：无法将节点添加到 Azure HDInsight 群集

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

无法将节点添加到 Azure HDInsight 群集。

## <a name="cause"></a>原因

原因可能有所不同。

## <a name="resolution"></a>分辨率

使用 "[群集大小](../hdinsight-scaling-best-practices.md)" 功能，计算群集所需的其他核心数。 此值基于新辅助角色节点中的核心总数。 然后尝试执行以下一个或多个步骤：

* 检查群集的位置中是否有任何可用的内核。

* 查看其他位置可用的核心数。 考虑在具有足够可用核心的其他位置重新创建群集。

* 若要提高特定位置的核心配额，请开具一份支持票证，指出想要提高 HDInsight 核心配额。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
