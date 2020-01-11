---
title: Apache Ambari UI 显示 Azure HDInsight 中的主机和服务
description: 当 Apache Ambari UI 问题显示 Azure HDInsight 中的主机和服务时，对其进行故障排除
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895631"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>方案： Apache Ambari UI 显示 Azure HDInsight 中的主机和服务

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

Apache Ambari UI 是可访问的，但 UI 显示几乎所有服务都已关闭，所有显示检测信号的主机都将丢失。

## <a name="cause"></a>原因

在大多数情况下，这是 Ambari 服务器未在活动头节点上运行的问题。 检查哪个头节点是活动的头节点，并确保 ambari 运行在正确的头节点上。 请勿手动启动 ambari-server，让故障转移控制器服务负责启动右头节点上的 ambari。 重新启动活动头节点以强制进行故障转移。

网络问题也可能导致此问题。 在每个群集节点中，查看是否可以 ping `headnodehost`。 在极少数情况下，任何群集节点都无法连接到 `headnodehost`：

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>分辨率

通常，重新启动活动头节点会缓解此问题。 如果不是，请与 HDInsight 支持团队联系。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -通过将 Azure 社区连接到适当的资源来改进客户体验的官方 Microsoft Azure 帐户：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
