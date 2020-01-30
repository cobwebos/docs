---
title: 群集创建失败，Azure HDInsight 中出现 DomainNotFound 错误
description: 与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能解决方法
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776230"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>方案：群集创建由于 Azure HDInsight 中的 DomainNotFound 错误而失败

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

HDI 安全（企业安全性套餐）群集创建失败，出现 `DomainNotFound` 错误消息。

## <a name="cause"></a>原因

DNS 设置不正确。

## <a name="resolution"></a>分辨率

部署已加入域的群集后，HDI 将在 AAD DS （对于每个群集）中创建内部用户名和密码，并将所有群集节点加入此域。 域加入是使用 Samba 工具来完成的。 请确保满足以下先决条件：

* 域名应通过 DNS 解析。
* 应在要部署群集的虚拟网络的 DNS 设置中设置域控制器的 IP 地址。
* 如果虚拟网络与 AAD DS 的虚拟网络对等互连，则必须手动执行此操作。
* 如果使用的是 DNS 转发器，则必须在虚拟网络中正确解析域名。
* 安全策略（Nsg）不应阻止加入域。

### <a name="additional-debugging-steps"></a>其他调试步骤

* 在同一子网中部署 windows VM，使用用户名和密码加入计算机（可以通过控制面板 UI 完成此操作），或

* 在同一子网中部署 ubuntu VM，并加入计算机
  * 通过 SSH 连接到计算机
  * sudo su
  * 用用户名和密码运行脚本
  * 脚本将进行 ping 操作，创建所需的配置文件，然后创建域。 如果成功，你的 DNS 设置就好了。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
