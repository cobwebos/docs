---
title: 使用 Azure HDInsight 对 HBase 进行故障排除
description: 获取有关使用 HBase 和 Azure HDInsight 的常见问题的解答。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573941"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 Apache HBase 进行故障排除

了解处理 Apache Ambari 中的 Apache HBase 有效负载时的最常见问题及其解决方法。

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>如何对多个未分配区域运行 hbck 命令报告？

运行 `hbase hbck` 命令时，可能会出现的一条常见错误消息是“多个区域未分配，或者区域链中出现漏洞”。

在 HBase Master UI 中，可以看到所有区域服务器中非均衡区域的数目。 然后，可以运行 `hbase hbck` 命令查看区域链中的漏洞。

漏洞可能是脱机区域造成的，因此请先修复分配问题。 

若要使未分配的区域恢复正常状态，请完成以下步骤：

1. 使用 SSH 登录到 HDInsight HBase 群集。
2. 若要与 Apache ZooKeeper shell 进行连接，请运行 `hbase zkcli` 命令。
3. 运行 `rmr /hbase/regions-in-transition` 命令或 `rmr /hbase-unsecure/regions-in-transition` 命令。
4. 若要从 `hbase zkcli` shell 退出，请使用 `exit` 命令。
5. 打开 Apache Ambari UI，并重启 Active HBase Master 服务。
6. 再次运行 `hbase hbck` 命令（不带任何选项）。 检查此命令的输出以确保分配了所有区域。


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>使用 hbck 命令进行区域分配时，如何解决超时问题？

### <a name="issue"></a>问题

使用 `hbck` 命令时出现超时问题的可能原因是多个区域长时间处于“正在转换”状态。 在 HBase Master UI 中可以看到这些区域显示为脱机。 由于有大量区域正在尝试进行转换，因此，HBase Master 可能会超时并且无法使那些区域恢复联机。

### <a name="resolution-steps"></a>解决步骤

1. 使用 SSH 登录到 HDInsight HBase 群集。
2. 若要与 Apache ZooKeeper shell 进行连接，请运行 `hbase zkcli` 命令。
3. 运行 `rmr /hbase/regions-in-transition` 或 `rmr /hbase-unsecure/regions-in-transition` 命令。
4. 若要退出 `hbase zkcli` shell，请使用 `exit` 命令。
5. 在 Ambari UI 中，重启 Active HBase Master 服务。
6. 再次运行命令 `hbase hbck -fixAssignments`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
