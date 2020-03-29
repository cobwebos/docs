---
title: 群集创建失败，Azure HDInsight 中域未找到错误
description: 在与 Azure HDInsight 群集交互时，对问题进行故障排除步骤和可能的解决方案
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776230"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>方案：群集创建失败，Azure HDInsight 中域未发现错误

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

HDI 安全（企业安全包）群集创建失败`DomainNotFound`，出现错误消息。

## <a name="cause"></a>原因

DNS 设置不正确。

## <a name="resolution"></a>解决方法

部署域加入群集时，HDI 会在 AAD DS 中创建内部用户名和密码（每个群集），并将所有群集节点联接到此域。 域联接使用 Samba 工具完成。 请确保满足以下先决条件：

* 域名应通过 DNS 解析。
* 域控制器的 IP 地址应在部署群集的虚拟网络的 DNS 设置中设置。
* 如果虚拟网络与 AAD DS 的虚拟网络对等，则必须手动完成。
* 如果您使用的是 DNS 转发器，则域名必须在虚拟网络中正确解析。
* 安全策略 （NSG） 不应阻止域加入。

### <a name="additional-debugging-steps"></a>其他调试步骤

* 在同一子网中部署窗口 VM，域使用用户名和密码加入计算机（这可以通过控制面板 UI 完成），或者

* 在同一子网中部署 ubuntu VM，并加入计算机
  * SSH 进入机器
  * sudo su
  * 使用用户名和密码运行脚本
  * 脚本将 ping，创建所需的配置文件，然后域。 如果成功，则 DNS 设置良好。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 用于改善客户体验的官方 Microsoft Azure 帐户连接。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
