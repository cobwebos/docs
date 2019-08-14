---
title: BindException-已在 Azure HDInsight 中使用的地址
description: BindException-已在 Azure HDInsight 中使用的地址
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947846"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>方案:BindException-已在 Azure HDInsight 中使用的地址

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache HBase 区域服务器上的重启操作无法完成。 在区域`region-server.log`服务器`/var/log/hbase`启动失败的辅助角色节点上的中, 你可能会看到如下错误消息:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>原因

在繁重的工作负荷活动期间重新启动 HBase 区域服务器。 下面是用户通过 Ambari UI 在 HBase 区域服务器上启动重启操作时, 幕后发生的情况:

1. Ambari 代理将向区域服务器发送停止请求。

1. 然后, Ambari 代理等待30秒, 使区域服务器正常关闭。

1. 如果应用程序继续与区域服务器进行连接, 则该应用程序将不会立即关闭, 因此, 30 秒的超时将很快过期。

1. 在 30 秒到期后，Ambari 代理将向区域服务器发送强制终止命令 (kill -9)。

1. 由于这种突然关闭, 尽管区域服务器进程已被终止, 但与该进程关联的端口可能没有释放, 最终导致`AddressBindException`。

## <a name="resolution"></a>解决

在启动重新启动之前, 请降低 HBase 区域服务器上的负载。

或者, 尝试使用以下命令在辅助角色节点上手动重新启动区域服务器:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
