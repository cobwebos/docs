---
title: 在 Apache HBase 群集中限定 CPU-Azure HDInsight
description: 排查 Azure HDInsight 中 Apache HBase 群集的区域服务器上的限定 CPU 问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887302"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中的 Apache HBase 群集中限定区域服务器上的 CPU

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache HBase 区域服务器进程开始占用接近200% 的 CPU，导致在 HBase Master 进程和群集上触发警报，使其不会以完整的容量运行。

## <a name="cause"></a>原因

如果运行的是 HBase 群集3.4 版，则可能是由于将 jdk 升级到版本 1.7.0 _151 而导致的潜在 bug。 我们发现的症状是：区域服务器进程开始占用接近200% 的 CPU （验证此运行 `top` 命令; 如果有接近 200% CPU 的进程，则通过运行 `ps -aux | grep`，确认它是区域服务器进程。

## <a name="resolution"></a>分辨率

1. 在群集的所有节点上安装 jdk 1.8，如下所示：

    * `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`运行脚本操作。 请确保选择要在所有节点上运行的选项。

    * 或者，你可以登录到每个单独的节点并运行 `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`命令。

1. 请参阅 Ambari UI-`https://<clusterdnsname>.azurehdinsight.net`。

1. 导航至**HBase-> 配置-> 高级 > 高级**`hbase-env configs`，并将变量 `JAVA_HOME` 更改为 `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`。 保存配置更改。

1. [可选但建议使用][刷新群集上的所有表](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

1. 再次从 Ambari UI 重新启动需要重新启动的所有 HBase 服务。

1. 群集到达稳定状态可能需要几分钟到一小时的时间，具体取决于群集上的数据。 确认群集达到稳定状态的方式是：从 Ambari （refresh）检查 HMaster UI （所有区域服务器都应处于活动状态），或从头节点运行 HBase shell，然后运行状态命令。

若要验证升级是否成功，请检查是否已使用相应的 java 版本（例如区域服务器检查的实例）启动相关的 HBase 进程：

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
