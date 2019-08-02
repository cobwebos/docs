---
title: Apache Hive 中的联接导致 Azure HDInsight 中的 OutOfMemory 错误
description: 处理 OutOfMemory 错误 "GC 开销限制超出错误"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 45b19bdfc12313974252c81c2597645b1ceb2018
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668841"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>方案:Apache Hive 中的联接导致 Azure HDInsight 中的 OutOfMemory 错误

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache Hive 联接的默认行为是将表的全部内容加载到内存中, 以便无需执行映射/化简步骤便可执行联接。 如果 Hive 表太大而无法放入内存中, 则查询可能会失败。

## <a name="cause"></a>原因

在具有足够大小的 hive 中运行联接时, 遇到以下错误:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>解决

通过设置以下 Hive 配置值, 防止 Hive 在联接 (而不是执行映射/化简步骤) 上将表加载到内存中:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>后续步骤

如果设置此值不能解决问题, 请访问以下其中一项 。

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
