---
title: 在 Azure HDInsight 中创建群集失败并出现 DomainNotFound 错误
description: 与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776230"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中创建群集失败并出现 DomainNotFound 错误

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

HDI 安全（企业安全性套餐）群集创建失败并出现 `DomainNotFound` 错误消息。

## <a name="cause"></a>原因

DNS 设置不正确。

## <a name="resolution"></a>解决方法

部署已加入域的群集后，HDI 会在 AAD DS 中（为每个群集）创建内部用户名和密码，并会将所有群集节点加入此域。 域加入使用 Samba 工具来完成。 请确保满足以下先决条件：

* 域名应通过 DNS 进行解析。
* 应在要部署群集的虚拟网络的 DNS 设置中设置域控制器的 IP 地址。
* 如果此虚拟网络与 AAD DS 的虚拟网络对等互连，则必须手动执行此操作。
* 如果使用的是 DNS 转发器，则必须在虚拟网络中正确解析域名。
* 安全策略 (NSG) 不应阻止域加入。

### <a name="additional-debugging-steps"></a>其他调试步骤

* 在同一子网中部署 Windows VM，使用用户名和密码将计算机加入域（这可以通过控制面板 UI 来完成），或者

* 在同一子网中部署 ubuntu VM，并将计算机加入域
  * 通过 SSH 连接到计算机
  * sudo su
  * 使用用户名和密码运行脚本
  * 脚本将进行 ping 操作，创建所需的配置文件，然后将计算机加入域。 如果成功，表明 DNS 设置正确。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
