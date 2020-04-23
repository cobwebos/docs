---
title: 排查 Azure HDInsight 中的资源创建失败问题
description: 本文中提供了常见的容量问题和缓解技术。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.openlocfilehash: 871e6b1253b0fc9a5b6ae5d3389982cfdd6af3bd
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103173"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>排查 Azure HDInsight 中的资源创建失败问题

本文介绍与 Azure HDInsight 交互时的问题疑难解答步骤和可能的解决方法。

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>错误：部署将超过 "800" 的配额

Azure 的配额限制为每个资源组 800 个部署。 将按资源组、订阅、帐户和其他范围应用不同的配额。 例如，订阅可能配置为限制某个区域的核心数目。 尝试使用比允许的内核数更多的虚拟机来部署错误消息，指出已超出配额。

若要解决此问题，请使用 Azure 门户、CLI 或 PowerShell 删除不再需要的部署。

有关详细信息，请参阅[解决资源配额错误](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>错误：最大节点已超过此区域中的可用内核数

订阅可能配置为限制某个区域的核心数目。 尝试使用比允许的更多的内核部署资源会产生错误消息，指出已超出配额。

若要请求增加配额，请按以下步骤操作：

1. 转到 [Azure 门户](https://portal.azure.com)并选择“帮助 + 支持”。****

1. 选择 "**新建支持请求**"。

1. 在“新建支持请求”页的“基本信息”选项卡上提供以下信息：********

   * **问题类型：** 选择**服务和订阅限制（配额）**。
   * **订阅：** 选择要修改的订阅。
   * **配额类型：** 选择 " **HDInsight**"。

有关详细信息，请参阅[创建支持票证来增加核心](hdinsight-capacity-planning.md#quotas)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
