---
title: 在 Apache HBase 群集中限定 CPU-Azure HDInsight
description: 排查 Azure HDInsight 中 Apache HBase 群集的区域服务器上的限定 CPU 问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 845307f24495090891812b4e945e202cdad47e71
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468334"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中的 Apache HBase 群集中限定区域服务器上的 CPU

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache HBase 区域服务器进程开始占用接近 200% 的 CPU 使用率，导致 HBase Master 进程中激发警报，并且群集无法以完整容量正常运行。

## <a name="cause"></a>原因

如果运行的是 HBase 群集 v3.4，则你可能遇到了将 JDK 升级到版本 1.7.0 _151 后出现的一个 bug。 我们观察到的症状是，区域服务器进程开始占用接近 200% 的 CPU 使用率（若要验证，请运行命令 `top`；如果进程占用了接近 200% 的使用率，请运行 `ps -aux | grep` 获取其 PID，并确认它是否为区域服务器进程）。

## <a name="resolution"></a>分辨率

1. 在群集的所有节点上按如下所示安装 JDK 1.8：

    * 运行脚本操作 `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh`。 确保选择在所有节点上运行的选项。

    * 或者，可以登录到每个节点并运行命令 `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk`。

1. 转到 Ambari UI - `https://<clusterdnsname>.azurehdinsight.net`。

1. 导航到“HBase”->“配置”->“高级”->“高级 **”，并将变量**  更改为 `hbase-env configs`。`JAVA_HOME``export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` 保存配置更改。

1. [可选但建议] [刷新群集上的所有表](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)。

1. 同样在 Ambari UI 中，重启所有需要重启的 HBase 服务。

1. 群集进入稳定状态可能需要几分钟到长达一小时的时间，具体取决于群集上的数据。 若要确认群集是否进入稳定状态，可以在 Ambari 中检查（刷新）HMaster UI（所有区域服务器都应处于活动状态），或者在头节点中运行 HBase shell，然后运行 status 命令。

若要验证升级是否成功，请检查是否已使用适当的 Java 版本启动相关的 HBase 进程 - 例如，按如下所示检查区域服务器：

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 对订阅管理和付费支持的访问包含在你的 Microsoft Azure 订阅中，而技术支持通过其中一个 [Azure 支持计划](https://azure.microsoft.com/support/plans/)进行提供。
