---

title: "从 Azure RemoteApp 迁移到 Citrix XenApp Essentials | Microsoft Docs"
description: "如何从 Azure RemoteApp 迁移到 Citrix XenApp Essentials"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>从 Azure RemoteApp 迁移到 Citrix XenApp Essentials

如果使用 Azure RemoteApp 并希望迁移到 Citrix XenApp Essentials，请记住几个先决条件。 首先，阅读 Citrix 的 [Citrix XenApp Essentials 的分步技术部署指南](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf)及其[联机技术库](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html)。 

## <a name="prerequisite-steps-for-migration"></a>迁移的必需步骤

1. 创建新的虚拟网络，或确定要将 Citrix XenApp Essentials 部署到 Azure Resource Manager 中的哪个 Azure 虚拟网络。 Azure RemoteApp 使用 Azure 经典门户；Citrix XenApp Essentials 仅支持 Azure Resource Manager。  
2. 确保已选择的虚拟网络可以通过网络访问域控制器，因为 Citrix 仅支持混合部署。 如果使用 Azure RemoteApp 的云部署，请确保虚拟网络可以通过网络访问 Active Directory 域控制器。 也可使用 Azure Active Directory 域服务 (Azure AD DS)。 
3. 确保已为虚拟网络正确配置 DNS，以便在首次尝试时即可成功加入域。 可在选择的虚拟网络中创建虚拟机 (VM)，然后执行手动域加入，验证 DNS 和域加入是否按预期发挥作用。 这将确保首次部署 Citrix XenApp Essentials 即可成功。 
4. 如果需要，可在配合 Azure RemoteApp 使用的 Azure 经典门户虚拟网络与 Azure Resource Manager 虚拟网络之间，创建虚拟网络对等互连。 如果两个网络位于同一区域，则此对等互连进程有效。 如果它们不在同一区域，请使用站点到站点 VPN 连接虚拟网络以实现网络连接。 
5. 如果需要，请阅读[如何将数据迁入和迁出 Azure RemoteApp](remoteapp-migrate.md)。 
6. 更新现有 Azure RemoteApp 映像以包括 Citrix VDA 组件（有关说明，请参阅 Citrix 文档）。 
7. 访问 Azure Marketplace 并开始 Citrix XenApp Essentials 部署。

## <a name="other-considerations"></a>其他注意事项

迁移时，请注意下列其他注意事项：
- Citrix XenApp Essentials 仅支持混合部署。 换而言之，它需要能够通过网络访问域控制器才能执行域加入。 如果使用 Azure RemoteApp 的云部署，请使用 Azure AD DS 或确保虚拟网络能够访问 Active Directory，以便执行域加入。 
- 若要了解如何将用户数据移到 Citrix XenApp Essentials，请参阅[如何将数据迁入和迁出 Azure RemoteApp](remoteapp-migrate.md)。 
- Citrix XenApp Essentials 仅支持 Active Directory 帐户。 不支持 Microsoft 帐户（如 outlook.com、msn.com 或 hotmail.com）。 

## <a name="citrix-xenapp-essentials-billing"></a>Citrix XenApp Essentials 计费

有关定价的完整详细信息，请参阅[常见问题解答](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699)和 [Citrix 概述文章](https://www.citrix.com/global-partners/microsoft/remote-app.html)。 Citrix XenApp Essentials 有三个计费组件：

- Citrix 服务费，每位用户每月 $12。 与所有的 Azure Marketplace 购买一样，这将通过与 Azure 订阅关联的付款方式收费。 对于企业协议 (EA) 客户，Azure 的资金信用额度无法使用。 
- 远程数据服务 (RDS) 客户端访问许可证 (CAL)。 目前，可以支付与 Citrix XenApp Essentials 付款捆绑销售的远程访问费 $6.25。 如果是 EA 客户，则可使用 Azure 的资金信用额度支付此项费用。 如果要使用现有的 RDS CAL，请与我们联系 ([arainfo@microsoft.com](mailto:arainfo@microsoft.com))，方便我们将其应用到你的帐单。 
- Azure 计算和存储。 这是所用 VM 的 Azure 存储成本和计算消耗。 请在选择 VM 大小和用户密度时注意定价。 如果是 EA 客户，则可使用 Azure 的资金信用额度支付此项费用。

如果仍有问题，可以：
- 发送电子邮件至 [arainfo@microsoft.com](mailto:arainfo@microsoft.com)。
- [联系 Azure 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 首先[打开 Azure 支持案例](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，这有助于完成必需的步骤 1-5。 对于步骤 6-7，通过在 Citrix 管理门户内打开支持票证可联系 Citrix。 

