---
title: 管理 Azure HDInsight 中的磁盘空间
description: 排查在与 Azure HDInsight 群集交互时遇到的问题的步骤和可能的解决方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77473007"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>管理 Azure HDInsight 中的磁盘空间

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="hive-log-configurations"></a>Hive 日志配置

1. 在 web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。

1. 导航到**Hive** > 配置 > **advanced** > **高级 Hive-log4j**。 查看下列设置：

    * `hive.root.logger=DEBUG,RFA` 列中的一个值匹配。 这是默认值，将[日志级别](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html)修改为 `INFO` 以打印更少的日志条目。

    * `log4jhive.log.maxfilesize=1024MB` 列中的一个值匹配。 这是默认值，根据需要进行修改。

    * `log4jhive.log.maxbackupindex=10` 列中的一个值匹配。 这是默认值，根据需要进行修改。 如果省略该参数，则生成的日志文件将是无限的。

## <a name="yarn-log-configurations"></a>Yarn 日志配置

查看以下配置：

* Apache Ambari

    1. 在 web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。

    1. 导航到**Hive** > 配置 > **高级** > **资源管理器**。 确保选中 "**启用日志聚合**"。 如果禁用，名称节点将本地保留日志，而不会在应用程序完成或终止时将它们聚合到远程存储中。

* 确保群集大小适合工作负荷。 工作负荷可能最近发生了更改，或者可能已调整了群集的大小。 [向上缩放](../hdinsight-scaling-best-practices.md)群集以匹配更高的工作负荷。

* 可能会用孤立的文件（如资源管理器重新启动）填充 `/mnt/resource`。 如有必要，请手动清理 `/mnt/resource/hadoop/yarn/log` 和 `/mnt/resource/hadoop/yarn/local`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
