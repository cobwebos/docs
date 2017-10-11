---
title: "在 Azure 安全中心中通过面向 Internet 的终结点限制访问 |Microsoft 文档"
description: "本文档演示如何实现 Azure 安全中心建议**通过面向 Internet 的终结点限制访问**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
ms.openlocfilehash: f7309c617f1705205e2c9f1b1b48d141391d45da
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>在 Azure 安全中心中通过面向 Internet 的终结点限制访问
如果任何网络安全组 (NSG) 具有一个或多个允许从“任何”源 IP 地址进行访问的入站规则，Azure 安全中心将建议通过面向 Internet 的终结点限制访问。 将访问权限打开到“任何”，攻击者能够访问资源。 安全中心将建议编辑这些入站规则，以限制对实际需要访问的源 IP 地址的访问。

此建议针对以“任何”作为源的任何非 Web 端口。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。 这并非一份循序渐进的指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡，选择“通过面向 Internet 的终结点限制访问”。

   ![通过面向 Internet 的终结点限制访问][1]
2. 这会打开“通过面向 Internet 的终结点限制访问”边栏选项卡。 此边栏选项卡列出了具有潜在安全问题的入站规则的虚拟机 (VM)。 选择 VM。

   ![选择 VM][2]
3. “NSG”边栏选项卡显示网络安全组信息、相关入站规则，以及相关联的 VM。 选择“编辑入站规则”以继续进行编辑入站规则。

   ![“网络安全组”边栏选项卡][3]
4. 在“入站安全规则”边栏选项卡中，选择要编辑的入站规则。 在此示例中，选择“AllowWeb”。

   ![入站安全规则][4]

   请注意，还可以选择“默认规则”以查看所有 NSG 都包含的默认规则集。 默认规则无法删除，但由于给它们分配的优先级较低，可以用创建的规则来重写它们。 详细了解[默认规则](../virtual-network/virtual-networks-nsg.md#default-rules)。

   ![默认规则][5]
5. 在“AllowWeb”边栏选项卡中，编辑入站规则的属性，以便**源**是 IP 地址或 IP 地址块。 若要了解有关的入站规则的属性的详细信息，请参阅 [NSG 规则](../virtual-network/virtual-networks-nsg.md#nsg-rules)。

   ![编辑入站规则][6]

## <a name="see-also"></a>另请参阅
本文演示如何实现 Azure 安全中心建议“通过面向 Internet 的终结点限制访问”。 若要了解有关启用 NSG 及规则的详细信息，请参阅以下内容：

* [什么是网络安全组 (NSG)？](../virtual-network/virtual-networks-nsg.md)
* [如何使用 Azure 门户管理 NSG](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理 Azure 安全中心中的安全建议](security-center-recommendations.md)-了解如何建议可以帮助你保护你的 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) - 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/) - 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
