---
title: 微软 Azure 的虚拟机先决条件 |Azure 应用商店
description: 列出了将 VM 产品/服务发布到 Azure 市场需要满足的先决条件。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277610"
---
# <a name="virtual-machine-prerequisites"></a>虚拟机先决条件

本文列出了在将 VM 产品/服务发布到[Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/)之前必须满足的技术要求和业务需求。  如果您尚未这样做，请查看[虚拟机产品供应发布指南](../../marketplace-virtual-machines.md)。


## <a name="technical-requirements"></a>技术要求

发布虚拟机 (VM) 解决方案的技术先决条件非常简单：

- 必须具有活动的 Azure 帐户。 如果没有，可以在 [Microsoft Azure 站点](https://azure.microsoft.com)进行注册。  
- 必须具有配置为支持 Windows 或 Linux VM 开发的环境。  有关详细信息，请参阅关联的 VM 文档站点：
    - [Linux VM 文档](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Windows VM 文档](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>业务要求

业务需求包括在规程、合同和法律方面的义务： 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- 你必须是已注册的云市场发布者。  如果未尚未注册，请执行[成为云市场发布者](https://docs.microsoft.com/azure/marketplace/become-publisher)一文中的步骤。

    > [!NOTE]
    > 应当使用相同的 Microsoft Developer Center 注册帐户登录到[云合作伙伴门户](https://cloudpartner.azure.com)。
    > 对于 Azure 市场产品/服务，只应具有一个 Microsoft 帐户。 它不应该特定于个体服务或产品。
    
- 您的公司（或其子公司）必须位于 Azure 应用商店支持的从国家/地区销售。  有关这些国家/地区的当前列表，请参阅 Microsoft [Azure 应用商店参与策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)。
- 产品必须以兼容 Azure 市场支持的计费模式的方式获得许可。  有关详细信息，请参阅[Azure 应用商店中的计费选项](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace)。 
- 你需要负责以商业上合理的方式向客户提供技术支持。 支持可以采用免费、付费或社区支持等多种方式。
- 负责获得自己的软件和任何第三方软件依赖项的许可。
- 提供的内容必须满足在 Azure 市场上和 Azure 门户中列入名单的产品/服务的标准。 <!-- TD: Meaning/links? -->
- 您必须同意[Microsoft Azure 应用商店参与策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)和发布者协议的条款。
- 您必须遵守[微软 Azure 网站使用条款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[微软隐私声明](https://privacy.microsoft.com/privacystatement)和[微软 Azure 认证计划协议](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。


## <a name="next-steps"></a>后续步骤

满足这些先决条件后，可以创建 VM[产品/服务](./cpp-create-offer.md)。
