---
title: JDBC/ODBC 和 Apache Thrift 框架问题 - Azure HDInsight
description: 无法在 Azure HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 软件框架下载大型数据集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: a8dcd6ae844810213ed6706002cdb9a31de94f60
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653582"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>无法在 HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 软件框架下载大型数据集

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件遇到问题时的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

尝试在 Azure HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 软件框架下载大型数据集时，收到如下错误消息：

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>原因

此异常由尝试使用超出允许的缓冲区空间的序列化进程引起。 在 Spark 2.0.0 中，通过 Apache Thrift 软件框架访问数据时，`org.apache.spark.serializer.KryoSerializer` 类用于序列化对象。 将通过网络发送或以序列化格式缓存的数据使用不同的类。

## <a name="resolution"></a>解决方法

增加 `Kryoserializer` 缓冲区值。 添加名为 `spark.kryoserializer.buffer.max` 的密钥，并在 spark2 配置的 `Custom spark2-thrift-sparkconf` 下将其设置为 `2047`。 重启所有受影响的组件。

> [!IMPORTANT]
> `spark.kryoserializer.buffer.max` 的值必须小于 2048。 不支持小数值。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区与适当的资源（解答、支持人员和专家）相关联来改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心 。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，技术支持将通过某个 [Azure 支持计划](https://azure.microsoft.com/support/plans/)提供。
