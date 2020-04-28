---
title: JDBC/ODBC & Apache Thrift framework 的问题-Azure HDInsight
description: 无法在 Azure HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 软件框架下载大型数据集
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894259"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>无法在 HDInsight 中使用 JDBC/ODBC 和 Apache Thrift software framework 下载大型数据集

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

在 Azure HDInsight 中尝试使用 JDBC/ODBC 和 Apache Thrift 软件框架下载大型数据集时，会收到如下所示的错误消息：

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>原因

此异常是由于序列化进程试图使用超出允许的缓冲区空间而导致。 在 Spark 2.0.0 中，类 `org.apache.spark.serializer.KryoSerializer` 用于在通过 Apache Thrift 软件框架访问数据时序列化对象。 其他类用于将通过网络发送或以序列化格式缓存的数据。

## <a name="resolution"></a>解决方法

增加 `Kryoserializer` 缓冲区值。 添加名为 `spark.kryoserializer.buffer.max` 的密钥，并在 `Custom spark2-thrift-sparkconf` 下的 spark2 配置中将其设置为 `2048`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
