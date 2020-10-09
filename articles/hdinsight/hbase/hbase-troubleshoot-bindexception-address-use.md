---
title: BindException - 地址已在 Azure HDInsight 中使用
description: BindException - 地址已在 Azure HDInsight 中使用
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887336"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>方案：BindException - 地址已在 Azure HDInsight 中使用

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

针对 Apache HBase 区域服务器执行的重启操作无法完成。 从区域服务器启动失败的工作器节点上 `/var/log/hbase` 目录中的 `region-server.log`，可能会看到如下错误消息：

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

在高工作负荷活动期间重启 Apache HBase 区域服务器。 以下是当用户通过 Apache Ambari UI 在 HBase 区域服务器上发起重启操作时，后台发生的情况：

1. Ambari 代理向区域服务器发送停止请求。

1. Ambari 代理等待 30 秒让区域服务器正常关闭

1. 如果应用程序继续与区域服务器进行连接，该服务器不会立即关闭。 在关闭之前，30 秒超时就会到期。

1. 30 秒之后，Ambari 代理向区域服务器发送强制终止 (`kill -9`) 命令。

1. 由于此关闭很突然，尽管区域服务器进程已被终止，但与该进程关联的端口可能还没有释放，这最终会导致 `AddressBindException`。

## <a name="resolution"></a>解决方法

在发起重启之前，减少 HBase 区域服务器上的负载。 另外，最好是先刷新所有表。 有关如何刷新表的参考信息，请参阅 [HDInsight HBase：如何通过刷新表改进 Apache HBase 群集重启时间](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

或者，尝试使用以下命令，手动重启工作器节点上的区域服务器：

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
