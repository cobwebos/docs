---
title: 无法在 Azure HDInsight 中读取 Apache Yarn 日志
description: 与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776191"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>方案：无法在 Azure HDInsight 中读取 Apache Yarn 日志

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

从存储帐户找到的 Apache Yarn 日志用户不可读。 文件分析程序无法工作，生成以下错误消息：

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn 日志聚合为文件分析程序不支持的 `IndexFile` 格式。

## <a name="resolution"></a>解决方法

1. 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。

1. 在 Ambari UI 中，导航到“YARN”   > “配置”   > “高级”   > “高级 yarn-site”  。

1. 对于 WASB 存储：`yarn.log-aggregation.file-formats` 的默认值为 `IndexedFormat,TFile`。 将该值更改为 `TFile`。

1. 对于 ADLS 存储：`yarn.nodemanager.log-aggregation.compression-type` 的默认值为 `gz`。 将该值更改为 `none`。

1. 保存更改并重启所有受影响的服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
