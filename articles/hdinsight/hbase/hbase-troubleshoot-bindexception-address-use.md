---
title: BindException-已在 Azure HDInsight 中使用的地址
description: BindException-已在 Azure HDInsight 中使用的地址
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887336"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>方案： BindException-已在 Azure HDInsight 中使用的地址

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache HBase 区域服务器上的重启操作无法完成。 从区域服务器启动失败的辅助角色节点上的 `/var/log/hbase` 目录中 `region-server.log`，可能会看到类似如下的错误消息：

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

在繁重的工作负荷活动期间重启 Apache HBase 区域服务器。 下面是用户在 HBase 区域服务器上的 Apache Ambari UI 中启动重新启动操作时，幕后发生的情况：

1. Ambari 代理向区域服务器发送停止请求。

1. Ambari 代理等待30秒以便区域服务器正常关闭。

1. 如果应用程序继续与区域服务器进行连接，该服务器不会立即关闭。 在关闭之前，30 秒超时就会到期。

1. 30 秒之后，Ambari 代理向区域服务器发送强制终止 (`kill -9`) 命令。

1. 由于此突然关闭，尽管区域服务器进程已被终止，但与该进程关联的端口可能没有释放，最终导致 `AddressBindException`。

## <a name="resolution"></a>分辨率

在启动重新启动之前，请降低 HBase 区域服务器上的负载。 另外，最好是先刷新所有表。 有关如何刷新表的参考信息，请参阅 [HDInsight HBase：如何通过刷新表来改善 Apache HBase 群集重启时间](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

或者，尝试使用以下命令手动重新启动辅助节点上的区域服务器：

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
