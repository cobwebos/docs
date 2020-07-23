---
title: Apache Hive 日志即将填满磁盘空间 - Azure HDInsight
description: Apache Hive 日志即将填满 Azure HDInsight 的头节点的磁盘空间。
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "78943967"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>方案：Apache Hive 日志即将填满 Azure HDInsight 的头节点的磁盘空间。

本文介绍了与 Azure HDInsight 群集的头节点上的磁盘空间不足相关的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

在 Apache Hive/LLAP 群集上，不需要的日志占用了头节点上的整个磁盘空间。 因此，可能会出现以下问题。

1. 由于头节点上没有剩余空间，SSH 访问失败。
2. Ambari 提供了 *HTTP 错误:503 服务不可用*。

发生此问题时，`ambari-agent` 日志会显示以下项。
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>原因

在高级 Hive-log4j 配置中，省略了参数 *log4j.appender.RFA.MaxBackupIndex*。 它会导致无限生成日志文件。

## <a name="resolution"></a>解决方法

1. 在 Ambari 门户上导航到 Hive 组件摘要，并单击 `Configs` 选项卡。

2. 转到“高级设置”中的 `Advanced hive-log4j` 部分。

3. 将 `log4j.appender.RFA` 参数设置为 RollingFileAppender。 

4. 如下所示设置 `log4j.appender.RFA.MaxFileSize` 和 `log4j.appender.RFA.MaxBackupIndex`。

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
5. 将 `hive.root.logger` 设置为 `INFO,RFA`，如下所示。 默认设置为 DEBUG，这会使日志变得非常大。

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. 保存配置并重启所需的组件。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
