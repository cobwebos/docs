---
title: Apache ZooKeeper 服务器无法在 Azure HDInsight 中形成仲裁
description: Apache ZooKeeper 服务器无法在 Azure HDInsight 中形成仲裁
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 05/20/2020
ms.openlocfilehash: 9038630a2623a8b20ddfcf98899ce9a89f16bdc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84673354"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper 服务器无法在 Azure HDInsight 中形成仲裁

本文介绍与 Azure HDInsight 群集中的 Zookeeper 相关的问题的故障排除步骤和可能的解决方案。

## <a name="symptoms"></a>症状

* 两个资源管理器都进入备用模式
* Namenode 都处于备用模式
* 由于 Zookeeper 连接失败，Spark、Hive 和 Yarn 作业失败，或 Hive 查询失败
* LLAP 守护程序无法在安全 Spark 或安全交互式 Hive 群集上启动

## <a name="sample-log"></a>示例日志

可能会在 yarn 日志（头节点上的 /var/log/hadoop-yarn/yarn/yarn-yarn*.log）中看到类似于以下内容的错误消息：

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>相关问题

* Yarn、NameNode 和 Livy 等高可用性服务可能会因为诸多原因而停止运行。
* 通过日志确认它与 Zookeeper 连接有关
* 确保问题反复发生（如果只是个别情况，请勿使用这些解决方案）
* 由于 Zookeeper 连接问题，作业可能会暂时失败

## <a name="common-causes-for-zookeeper-failure"></a>Zookeeper 失败的常见原因

* Zookeeper 服务器上的 CPU 使用率较高
  * 在 Ambari UI 中，如果看到 Zookeeper 服务器上的 CPU 持续使用率接近 100%，则在这段时间内打开的 Zookeeper 会话可能会过期并超时
* Zookeeper 客户端报告频繁超时
  * 在资源管理器、Namenode 等的日志中，将经常看到客户端连接超时
  * 这可能导致仲裁丢失、频繁的故障转移和其他问题

## <a name="check-for-zookeeper-status"></a>检查 Zookeeper 状态

* 从 /etc/hosts 文件或 Ambari UI 中找到 Zookeeper 服务器
* 运行以下命令
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181`（或 2182）  
  * 端口 2181 是 Apache Zookeeper 实例
  * HDInsight Zookeeper 使用端口 2182（为本身并非 HA 的服务提供 HA）
  * 如果命令未显示任何输出，则表明 Zookeeper 服务器未运行
  * 如果服务器正在运行，则结果将包括客户端连接的静态变量和其他统计信息

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>每小时 CPU 负载达到峰值

* 登录到 Zookeeper 服务器并检查 /etc/crontab
* 如果此时有任何每小时作业正在运行，请在不同的 Zookeeper 服务器之间随机分配启动时间。
  
## <a name="purging-old-snapshots"></a>清除旧快照

* 将 Zookeeper 配置为自动清除旧快照
* 默认情况下，保留最近的 30 个快照
* 保留的快照数量由配置项 `autopurge.snapRetainCount` 控制。 可在以下文件中找到此属性：
  * `/etc/zookeeper/conf/zoo.cfg`（适用于 Hadoop Zookeeper）
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg`（适用于 HDInsight Zookeeper）
* 将 `autopurge.snapRetainCount` 设置为值 3，然后重启 Zookeeper 服务器
  * 可以更新 Hadoop Zookeeper 配置，并且可以通过 Ambari 重启服务
  * 手动停止并重启 HDInsight Zookeeper
    * `sudo lsof -i :2182` 将显示要终止的进程 ID
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* 请勿手动清除快照，手动删除快照可能会导致数据丢失

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>Zookeeper 服务器日志中的 CancelledKeyException 不需要清理快照

* 此异常将在 zookeeper 服务器（/var/log/zookeeper/zookeeper-zookeeper-* 或 /var/log/hdinsight-zookeeper/zookeeper* 文件）上出现
* 此异常通常表示客户端不再处于活动状态，并且服务器无法发送消息
* 此异常还表示 Zookeeper 客户端过早结束了会话
* 查找本文档中所述的其他症状

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。
- 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。
- 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
