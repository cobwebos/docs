---
title: Apache Hive 日志填满磁盘空间-Azure HDInsight
description: Apache Hive 日志会在 Azure HDInsight 中的头节点上填满磁盘空间。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943967"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>方案： Apache Hive 日志在 Azure HDInsight 中的头节点上填满磁盘空间

本文介绍与 Azure HDInsight 群集中头节点上的磁盘空间不足相关的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

在 Apache Hive/LLAP 群集上，不需要的日志占用头节点上的整个磁盘空间。 因此，可能会出现以下问题。

1. 由于头节点上没有剩余空间，SSH 访问失败。
2. Ambari 提供*HTTP 错误：503服务不可用*。

发生此问题时，`ambari-agent` 日志会显示以下各项。
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

在高级 Hive-log4j 配置中，忽略参数*log4j* 。 它会导致日志文件的无限生成。

## <a name="resolution"></a>解决方法

1. 在 Ambari 门户上导航到 "Hive 组件摘要"，并单击 `Configs` "选项卡。

2. 中转到 "高级设置" 中的 "`Advanced hive-log4j`" 部分。

3. 将 `log4j.appender.RFA` 参数设置为 RollingFileAppender。 

4. 按如下所示设置 `log4j.appender.RFA.MaxFileSize` 和 `log4j.appender.RFA.MaxBackupIndex`。

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. 将 `hive.root.logger` 设置为 `INFO,RFA`，如下所示。 默认设置为 "调试"，这会使日志变得非常大。

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. 保存配置并重新启动所需的组件。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
