---
title: Apache Hive 连接到 Apache Zookeeper-Azure HDInsight
description: 由于 Azure HDInsight 中的 Apache Zookeeper 问题，Apache Hive 查看不可访问
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: a6ede13ea258048857e06e1c158a297219ff0df6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494280"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>方案： Apache Hive 无法在 Azure HDInsight 中建立与 Apache Zookeeper 的连接

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Hive 视图不可访问，`/var/log/hive` 中的日志显示类似于以下内容的错误：

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>原因

Hive 可能无法建立与 Zookeeper 的连接，这会阻止 Hive 视图启动。

## <a name="resolution"></a>分辨率

1. 检查 Zookeeper 服务是否正常运行。

1. 检查 Zookeeper 服务是否具有 Hive Server2 的 ZNode 条目。 该值会缺失或不正确。

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 若要重新建立连接，请重新启动 Zookeeper 节点，并重新启动 HiveServer2。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 对订阅管理和付费支持的访问包含在你的 Microsoft Azure 订阅中，而技术支持通过其中一个 [Azure 支持计划](https://azure.microsoft.com/support/plans/)进行提供。
