---
title: "在 Azure 安全中心中添加下一代防火墙 | Microsoft 文档"
description: "本文档演示如何实现 Azure 安全中心建议**添加下一代防火墙** 和**仅通过 NGFW 路由流量**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cc9541251157a3c8c632a01fafb52ea87ba58dd


---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>在 Azure 安全中心中添加下一代防火墙
Azure 安全中心可能会建议从 Microsoft 合作伙伴添加下一代防火墙 (NGFW)，以增强安全保护。 本文档将举例说明如何执行此操作。

> [!NOTE]
> 本文档通过使用示例部署介绍此服务。  这并非一份循序渐进的指南。
> 
> 

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡，选择“添加下一代防火墙”。
   ![添加下一代防火墙][1]
2. 在“添加下一代防火墙”边栏选项卡，选择一个终结点。
   ![选择终结点][2]
3. 另一个“添加下一代防火墙”边栏选项卡将打开。 你可以选择使用现有解决方案（如果可用），或者可以创建一个新的解决方案。 在此示例中，没有任何可用的现有解决方案，因此我们将创建一个新的 NGFW。
   ![创建新的下一代防火墙][3]
4. 若要创建新的 NGFW，请从集成的合作伙伴的列表中选择一种解决方案。 在此示例中，我们将选择“检查点”。
   ![选择下一代防火墙解决方案][4]
5. “检查点”边栏选项卡将打开，并提供有关该合作伙伴解决方案的信息。 在信息边栏选项卡选择“创建”。
   ![防火墙信息边栏选项卡][5]
6. 将打开“创建虚拟机”边栏选项卡。 在此可以输入旋转加速将运行 NGFW 的虚拟机 (VM) 所需的信息。 按照这些步骤并提供所需的 NGFW 信息。 选择“确定”以应用。
   ![创建要运行 NGFW 的虚拟机][6]

## <a name="route-traffic-through-ngfw-only"></a>仅通过 NGFW 路由流量
返回到“建议”边栏选项卡。 通过安全中心添加 NGFW 后生成新的项，调用**仅通过 NGFW 路由流量**。 只有在通过安全中心安装了 NGFW 时创建此建议。 如果有面向 Internet 的终结点，安全中心将建议配置强制入站流量通过 NGFW 到 VM 的网络安全组规则。

1. 在“建议”边栏选项卡，选择“仅通过 NGFW 路由流量”。
   ![仅通过 NGFW 路由流量][7]
2. 这将打开“仅通过 NGFW 路由流量”边栏选项卡，它列出了可以将流量路由到的 VM。 从列表中选择 VM。
   ![选择 VM][8]
3. 此时将打开所选 VM 的边栏选项卡，并显示相关的入站规则。 说明为你提供了可能的后续步骤的详细信息。 选择“编辑入站规则”以继续进行编辑入站规则。 预期结果是与 NGFW 链接的面向 Internet 的终结点的**源**未设置为**任何**。 若要了解有关的入站规则的属性的详细信息，请参阅 [NSG 规则](../virtual-network/virtual-networks-nsg.md#nsg-rules)。
   ![配置规则，以便限制访问][9]
   ![编辑入站规则][10]

## <a name="see-also"></a>另请参阅
本文档演示如何实现安全中心建议“添加下一代防火墙”。 若要了解有关 NGFW 和检查点合作伙伴解决方案的详细信息，请参阅以下内容：

* [下一代防火墙](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [检查点 vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心设置安全策略](security-center-policies.md) - 了解如何配置安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png



<!--HONumber=Nov16_HO3-->


