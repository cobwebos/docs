---
title: 在 Azure HDInsight 中启动服务时的端口冲突
description: 排查在与 Azure HDInsight 群集交互时遇到的问题的步骤和可能的解决方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 4cb0d464a82d8da0a09f5391eb1d06dfacd84290
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776217"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中启动服务时端口冲突

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

服务启动失败。

## <a name="cause"></a>原因

存在端口冲突。

## <a name="resolution"></a>分辨率

### <a name="method-1"></a>方法 1

使用以下命令可获取/关闭所有正在运行的进程，这些进程受端口问题的影响。

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

然后启动服务。

### <a name="method-2"></a>方法 2

重新启动节点。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
