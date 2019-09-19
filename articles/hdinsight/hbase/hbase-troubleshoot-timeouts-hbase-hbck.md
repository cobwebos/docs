---
title: Azure HDInsight 中包含 "hbase hbck" 命令的超时
description: 修复区域分配时 "hbase hbck" 命令出现超时问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 800182498ab77993d0861c9b5383e0d71b302b6f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091558"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>场景：Azure HDInsight 中包含 "hbase hbck" 命令的超时

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

修复区域分配`hbase hbck`时，遇到命令超时。

## <a name="cause"></a>原因

使用 `hbck` 命令时出现超时问题的可能原因是多个区域长时间处于“正在转换”状态。 在 HBase Master UI 中可以看到这些区域显示为脱机。 由于有大量区域正在尝试进行转换，HBase Master 可能会超时，无法使这些区域恢复联机状态。

## <a name="resolution"></a>分辨率

1. 使用 SSH 登录到 HDInsight HBase 群集。

1. 运行`hbase zkcli`命令，以 Apache ZooKeeper shell 进行连接。

1. 运行`rmr /hbase/regions-in-transition` 或`rmr /hbase-unsecure/regions-in-transition`命令。

1. `hbase zkcli` 使用`exit`命令从 shell 退出。

1. 在 Apache Ambari UI 中，重新启动 Active HBase Master 服务。

1. 运行 `hbase hbck -fixAssignments` 命令。

1. 监视 "转换中的 HBase Master UI" 区域，以确保不会阻止任何区域。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

- [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

- 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
