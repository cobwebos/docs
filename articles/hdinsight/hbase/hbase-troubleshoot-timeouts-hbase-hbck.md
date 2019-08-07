---
title: Azure HDInsight 中包含 "hbase hbck" 命令的超时
description: 修复区域分配时 "hbase hbck" 命令出现超时问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737922"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>方案:Azure HDInsight 中包含 "hbase hbck" 命令的超时

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

修复区域分配`hbase hbck`时, 遇到命令超时。

## <a name="cause"></a>原因

此处的可能原因可能是长时间内有多个处于“正在转换”状态的区域。 可从 Apache HBase Master UI 中将这些区域视为脱机状态。 由于有大量区域正在尝试进行转换, HBase Master 可能会超时, 无法将这些区域恢复为联机状态。

## <a name="resolution"></a>解决

1. 使用 SSH 登录到 HDInsight HBase 群集。

1. 运行`hbase zkcli`命令以连接到 zookeeper shell。

1. 运行`rmr /hbase/regions-in-transition` 或`rmr /hbase-unsecure/regions-in-transition`命令。

1. `hbase zkcli` 使用`exit`命令从 shell 退出。

1. 打开 Ambari UI 并从 Ambari 重启 Active HBase Master 服务。

1. 监视 "转换中的 HBase Master UI" 区域, 以确保不会阻止任何区域。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
