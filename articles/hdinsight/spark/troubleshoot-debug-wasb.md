---
title: 调试 Azure HDInsight 中的 WASB 文件操作
description: 描述在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.openlocfilehash: 1256575eea7ee80b41a875c6bcd9b281b98aa360
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163845"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>调试 Azure HDInsight 中的 WASB 文件操作

有时，你可能想要了解 WASB 驱动程序在 Azure 存储中启动的操作。 对于客户端，WASB 驱动程序在**调试**级别生成每个文件系统操作的日志。 WASB 驱动程序使用 log4j 来控制日志记录级别，默认值为**INFO** level。 有关 Azure 存储服务器端分析日志，请参阅[Azure 存储分析日志记录](../../storage/common/storage-analytics-logging.md)。

生成的日志将如下所示：

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>打开文件操作的 WASB 调试日志

1. 在 web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是 Spark 群集的名称。

1. 导航到**custom-spark2-defaults** > **配置** > **advanced custom-spark2-defaults-log4j**。

1. 修改 `log4j.appender.console.Threshold=INFO` 以 `log4j.appender.console.Threshold=DEBUG`。

1. 导航到**Advanced livy2-log4j**。

1. 添加以下属性：

    ```
    log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG
    ```

1. 保存更改。

## <a name="additional-logging"></a>其他日志记录

上述日志应提供对文件系统操作的高级理解。 如果上述日志仍未提供有用的信息，或者要调查 blob 存储 api 调用，请将 `fs.azure.storage.client.logging=true` 添加到 `core-site`中。 此设置将为 wasb 存储驱动程序启用 java sdk 日志，并将每次调用都打印给 blob 存储服务器。 删除调查后的设置，因为它可能会快速填满磁盘，并可能会减慢进程速度。

如果后端 Azure Data Lake 基于，则为组件使用以下 log4j 设置（例如，spark/tez/hdfs）：

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

在日志中查找日志 `/var/log/adl/adl.log`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
