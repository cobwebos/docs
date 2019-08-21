---
title: Azure HDInsight 中的区域服务器问题
description: Azure HDInsight 中的区域服务器问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/16/2019
ms.openlocfilehash: 6e734a661557b024257fcd1b9d9c2da6a3bc8f85
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640226"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight 中的区域服务器问题

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="scenario-unassigned-regions"></a>场景：未分配区域

### <a name="issue"></a>问题

运行`hbase hbck`命令时, 会显示类似于以下内容的错误消息:

```
multiple regions being unassigned or holes in the chain of regions
```

从 Apache HBase Master UI, 你可以看到所有区域服务器之间不均衡的区域数。 然后，可以运行 `hbase hbck` 命令查看区域链中的漏洞。

### <a name="cause"></a>原因

可能是由于脱机区域导致的。

### <a name="resolution"></a>分辨率

修复分配。 请遵循以下步骤使未分配的区域恢复为正常状态：

1. 使用 SSH 登录到 HDInsight HBase 群集。

1. 运行`hbase zkcli`命令以连接到 ZooKeeper shell。

1. 运行`rmr /hbase/regions-in-transition` 或`rmr /hbase-unsecure/regions-in-transition`命令。

1. 使用`exit`命令退出 zookeeper shell。

1. 打开 Apache Ambari UI，并重启 Active HBase Master 服务。

1. 再次`hbase hbck`运行命令 (无任何其他选项)。 检查输出并确保分配了所有区域。

---

## <a name="scenario-dead-region-servers"></a>场景：死区服务器

### <a name="issue"></a>问题

区域服务器无法启动。

### <a name="cause"></a>原因

多个拆分的 WAL 目录。

1. 获取当前 Wal 的列表: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`。

1. 检查该`wals.out`文件。 如果拆分目录太多 (从 * 拆分后开始), 则区域服务器可能会因为这些目录而失败。

### <a name="resolution"></a>分辨率

1. 从 Ambari 门户停止 HBase。

1. 执行`hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`以获取 wal 的最新列表。

1. 将 * 拆分的目录移动到临时文件夹, `splitWAL`并删除 * 拆分的目录。

1. 执行`hbase zkcli`命令以连接到 zookeeper shell。

1. 执行 `rmr /hbase-unsecure/splitWAL`。

1. 重新启动 HBase 服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
