---
title: InvalidNetworkSecurityGroupSecurityRules-Azure HDInsight 中的群集失败
description: 群集创建失败，错误代码为 InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: a534ae54b6cb9ce20d0a1bc970ef575f88a09827
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735750"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>场景：InvalidNetworkSecurityGroupSecurityRules-Azure HDInsight 中的群集创建失败

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

你会收到错误`InvalidNetworkSecurityGroupSecurityRules`代码，其说明类似于 "配置有子网的网络安全组中的安全规则不允许所需的入站和/或出站连接"。

## <a name="cause"></a>原因

可能是为群集配置的入站[网络安全组](../../virtual-network/virtual-network-vnet-plan-design-arm.md)规则有问题。

## <a name="resolution"></a>分辨率

中转到 Azure 门户，并确定与部署群集的子网关联的 NSG。 在 "**入站安全规则**" 部分中，确保规则允许对[此处](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)提到的 IP 地址进行入站访问端口443。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
