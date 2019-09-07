---
title: Apache Ambari UI 显示 Azure HDInsight 中的主机和服务
description: 当 Apache Ambari UI 问题显示 Azure HDInsight 中的主机和服务时，对其进行故障排除
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: bc65387252fd425256684474f38ec5d783bb45e7
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734854"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>场景：Apache Ambari UI 显示 Azure HDInsight 中的主机和服务

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache Ambari UI 是可访问的，但 UI 显示几乎所有服务都已关闭，所有显示检测信号的主机都将丢失。

## <a name="cause"></a>原因

在大多数情况下，这是 Ambari 服务器未在活动头节点上运行的问题。 检查哪个头节点是活动的头节点，并确保 ambari 运行在正确的头节点上。 请勿手动启动 ambari-server，让故障转移控制器服务负责启动右头节点上的 ambari。 重新启动活动头节点以强制进行故障转移。

网络问题也可能导致此问题。 从每个群集节点，查看是否可以进行`headnodehost`ping 操作。 在极少数情况下，任何群集节点都无法连接`headnodehost`到：

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>分辨率

通常，重新启动活动头节点会缓解此问题。 如果不是，请与 HDInsight 支持团队联系。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
