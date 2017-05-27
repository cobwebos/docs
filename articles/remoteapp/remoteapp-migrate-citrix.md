---

title: "如何从 Azure RemoteApp 迁移到 Citrix XenApp Essentials | Microsoft Docs"
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
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>如何从 Azure RemoteApp 迁移到 Citrix XenApp Essentials

希望迁移到 Citrix XenApp Essentials 的 Azure RemoteApp 客户需要满足几个先决条件。  建议先阅读 Citrix 的 [Citrix XenApp Essentials 的分步技术部署指南](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf)及其[联机技术库](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html)。 

幸运的是可以重复使用已在 Azure RemoteApp 中投入的大部分资本，但对于部署 XenApp Essentials 而言还存在几个高级先决条件。

## <a name="prerequisites"></a>先决条件

1. 创建新的 VNET，或确定要将 Citrix XenApp Essentials 部署到 Azure Resource Manager 中的哪个 Azure VNET。 Azure RemoteApp 使用 Azure 经典部署；Citrix XenApp Essentials 仅支持 Azure Resource Manager。  
2. 确保已选择的 VNET 可以通过网络访问域控制器，因为 Citrix 仅支持混合部署。 如果使用的是 Azure RemoteApp 的云部署，则需要确保 VNET 可以通过网络访问 Active Directory 域控制器，否则我们建议使用 Azure Active Directory 域服务 (AAD-DS)。 
3. 确保已为 VNET 正确配置了 DNS，从而在第一次尝试时即可成功加入域。 可以在选择的 VNET 中创建虚拟机，然后执行手动域加入以验证 DNS 和域加入是否按预期发挥作用。 这将确保首次部署 Citrix XenApp Essentials 时成功。 
4. 如果需要，请在与 Azure RemoteApp 配合使用的 Azure 经典 VNET 和 Azure Resource Manager VNET 之间创建 VNET 对等互连（前提是这些 VNET 位于同一区域；如果它们不在同一区域，则使用 S2S VPN 来连接 VNET 以进行联网）。 
5. 如果需要，请阅读[如何将数据迁入和迁出 Azure RemoteApp](remoteapp-migrate.md)。 
6. 更新现有 Azure RemoteApp 映像以包括 Citrix VDA 组件、有关说明的 Citrix 参考文档。 
7. 访问 Azure 应用商店并开始 Citrix XenApp Essentials 部署。

祝你好运，感谢使用 Azure RemoteApp。 

## <a name="other-considerations"></a>其他注意事项：

- Citrix XenApp Essentials 仅支持混合部署。 换而言之，它需要能够通过网络访问域控制器才能执行域加入。 如果使用的是 Azure RemoteApp 的云部署，则需要使用 AAD-DS 或确保 VNET 能够访问 Active Directory 以执行域加入。 
- 若要了解如何将用户数据移到 CXE，请阅读[如何将数据迁入和迁出 Azure RemoteApp](remoteapp-migrate.md)。 
- Citrix XenApp Essentials 仅支持 Active Directory 帐户。 它不支持 Microsoft 帐户（@outlook.com、@msn.com、@hotmail.com 等）。 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>了解 Citrix XenApp Essentials 的计费 

阅读[常见问题解答](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699)和 [Citrix 概述文章](https://www.citrix.com/global-partners/microsoft/remote-app.html)，了解有关定价的完整详细信息。 Citrix XenApp Essentials 的计费组件有三个，它们是：

1. Citrix 每个用户每月的服务费 $12。 与所有的 Azure 应用商店购买一样，这将通过与 Azure 订阅关联的付款方式收费。 对于 EA 客户，Azure 的资金信用额度无法使用。 
2. RDS CAL。 必须有自己的 RDS CAL（即将推出），或支付与 Citrix XenApp Essentials 付款捆绑的远程访问费 (RAF) $6.25 美元。 对于 EA 客户，Azure 的资金信用额度可用于支付此项费用。 如果要使用现有的 RDS CAL，请与我们联系 [arainfo@microsoft.com] (mailto:arainfo@microsoft.com)，以便我们可以将其应用到你的帐单。 
3. Azure 计算和存储。 这是所用 VM 的 Azure 存储成本和计算消耗。 请在选择 VM 大小和用户密度时注意定价。 对于 EA 客户，Azure 的资金信用额度可用于支付此项费用

如果仍有疑问，请与我们联系
1. 发送电子邮件至 [arainfo@microsoft.com](mailto:arainfo@microsoft.com)。
2. [联系 Azure 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 首先[建立 Azure 支持案例](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以帮助完成上面的步骤 #1-#5。 通过在 Citrix 管理门户内建立支持票证来联系 Citrix，以便完成步骤 #6-7。 

