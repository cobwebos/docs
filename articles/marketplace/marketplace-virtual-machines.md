---
title: Azure 市场的“虚拟机”产品/服务发布指南
description: 本文介绍发布要从市场部署的虚拟机和软件免费试用版的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687495"
---
# <a name="virtual-machine-offer-publishing-guide"></a>“虚拟机”产品/服务发布指南

虚拟机映像是在 Azure 市场中发布解决方案的主要方式之一。 使用本指南了解此产品/服务的要求。 

以下是通过市场部署和计费的事务产品/服务。 用户看到的号召性用语是“立即获取”。

## <a name="free-trial"></a>免费试用版 

使用自带许可 (BYOL) 计费模型时，你可以安排用户通过访问有限期限软件许可证来测试产品/服务。 

## <a name="test-drive"></a>体验版

通过基础结构即服务 (IaaS) 或软件即服务 (SaaS) 应用部署一个或多个虚拟机。 “体验版”发布选项的优点是可以根据合作伙伴托管的指导教程，实现对虚拟机或整个解决方案环境的自动预配。 体验版提供评估，且不向客户收取额外的费用。 客户无需是现有 Azure 客户，即可参与试用体验。 

若要开始，请通过 [amp-testdrive](mailto:amp-testdrive@microsoft.com) 与我们联系。 

|要求  |详细信息 |
|---------|---------|
| 已安装市场应用   |    通过 IaaS 或 SaaS 部署的一个或多个虚拟机。      |

## <a name="interactive-demo"></a>互动演示

通过互动演示，为客户体验引导式解决方案体验。 “互动演示”发布选项的优点是提供试用体验，且无需为复杂解决方案进行复杂预配。 

## <a name="virtual-machine-offer"></a>“虚拟机”产品/服务

将虚拟设备部署到与客户关联的订阅时，请使用“虚拟机”产品/服务类型。 使用即用即付或自带许可 (BYOL) 许可模型为 VM 启用完整商务功能。 Microsoft 托管商业事务并代你向客户计费。 可获得的好处是能够利用客户与 Microsoft 之间达成的首选付费关系（包括任何企业协议）。

> [!NOTE]
> 目前可针对 VM 的 Azure 使用情况，而非软件许可费用，使用与企业协议关联的货币承诺。  
> 
> [!NOTE]
> 通过将映像和定价发布为“专有”套餐，可将 VM 的发现和部署限制为特定的一组客户。 “专有”产品/服务能够为关系最密切的客户提供专属优惠，并提供自定义软件和条款。 这些自定义条款可在一系列场景中提供助力，包括使用特殊定价和条款的现场导向型交易以及对限制性版本软件的早期访问。 “专有”套餐可让你通过创建包含这些细节的新 SKU，向有限的一组客户提供特定的定价或产品。  
> *   有关专有产品/服务的详细信息，请访问“Azure 市场中的专有产品/服务”页 [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)。  

| 要求 | 详细信息 |  
|:--- |:--- | 
| 计费和计量 | VM 必须支持 BYOL 或即用即付每月计费。 |  
| 与 Azure 兼容的虚拟硬盘 (VHD) | 必须基于 Windows 或 Linux 构建 VM。 <ul> <li>有关创建 Linux VHD 的详细信息，请参阅 [Azure 认可的 Linux 发行版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。</li> <li>有关创建 Windows VHD 的详细信息，请参阅[创建与 Azure 兼容的 VHD](./partner-center-portal/azure-vm-create-offer.md)。</li> </ul> |  

>[!Note]
>现在提供了云解决方案提供商（CSP）合作伙伴渠道选择。  请参阅[云解决方案提供商](./cloud-solution-providers.md)，了解有关通过 Microsoft CSP 合作伙伴渠道营销产品/服务的详细信息。

## <a name="next-steps"></a>后续步骤

如果尚未注册， 

- [了解](https://azuremarketplace.microsoft.com/sell)marketplace。

如果已注册并正在创建新套餐或正在使用现有套餐，

- [登录到合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，创建或完成产品/服务。
- 有关详细信息，请参阅[创建虚拟机产品/服务](./partner-center-portal/azure-vm-create-offer.md)。
