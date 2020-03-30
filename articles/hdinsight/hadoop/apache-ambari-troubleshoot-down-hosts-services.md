---
title: Apache Ambari UI 显示 Azure HDInsight 中的主机和服务已关闭
description: 在 Azure HDInsight 中显示主机和服务时，解决 Apache Ambari UI 问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895631"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>方案：Apache Ambari UI 在 Azure HDInsight 中向下显示主机和服务

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Apache Ambari UI 可以访问，但 UI 显示几乎所有服务都已关闭，所有主机都显示检测信号丢失。

## <a name="cause"></a>原因

在大多数情况下，这是 Ambari 服务器未在活动头节点上运行的问题。 检查哪个头节点是活动头节点，并确保 Ambari 服务器在正确的头节点上运行。 请勿手动启动 Ambari 服务器，让故障转移控制器服务负责在正确的头节点上启动 Ambari 服务器。 重新启动活动头节点以强制进行故障转移。

网络问题也可能导致此问题。 从每个群集节点，看看是否可以 ping 通 `headnodehost`。 在极少数情况下，任何群集节点都无法连接到 `headnodehost`：

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>解决方法

通常，重新启动活动头节点会缓解此问题。 如果未缓解，请与 HDInsight 支持团队联系。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帐户连接 Azure 社区，以将 Azure 社区连接到正确的资源：答案、支持和专家，从而改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
