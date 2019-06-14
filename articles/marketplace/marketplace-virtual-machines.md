---
title: Azure 市场的“虚拟机”产品/服务发布指南
description: 本文介绍发布要从市场部署的虚拟机和软件免费试用版的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ccb6fc9c522e8d05d0184fc5e248d070efb9921d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64937725"
---
# <a name="virtual-machine-offer-publishing-guide"></a>“虚拟机”产品/服务发布指南

虚拟机映像是在 Azure 市场中发布解决方案的主要方式之一。 使用本指南了解此产品/服务的要求。 

以下是通过市场部署和计费的事务产品/服务。 用户看到的号召性用语是“立即获取”。

## <a name="free-trial"></a>免费试用版 

使用自带许可 (BYOL) 计费模型时，你可以安排用户通过访问有限期限软件许可证来测试产品/服务。 下面是部署此产品/服务的要求。 

|要求  |详细信息  |
|---------|---------|
|免费试用期和试用体验     |   客户可以在有限时间内免费试用你的应用。 客户不需要支付产品/服务的任何许可证或订阅费用。 客户不需要支付基础 Microsoft 第一方产品或服务的费用。 所有试用选项部署到 Azure 订阅。 你可以独自控制成本优化和管理。 可以选择免费试用或互动演示。 不管选择哪种，免费试用版都必须向客户提供规定时间的免费产品/服务试用。|
|可轻松配置、随时可用的解决方案    |  应用的配置和设置须便捷。       |
|可用性/运行时间    |    SaaS 应用或平台的运行时间须不低于 99.9%。     |
|Azure Active Directory     |    在已启用许可的情况下，产品/服务须允许使用 Azure Active Directory (Azure AD) 联合单一登录 (SSO)（Azure AD 联合 SSO）。     |

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
> 通过将映像和定价发布为“专有”产品/服务，可将 VM 的发现和部署限制为特定的一组客户。 “专有”产品/服务能够为关系最密切的客户提供专属优惠，并提供自定义软件和条款。 这些自定义条款可在一系列场景中提供助力，包括使用特殊定价和条款的现场导向型交易以及对限制性版本软件的早期访问。 “专有”产品/服务可让你通过创建包含这些细节的新 SKU，向有限的一组客户提供特定的定价或产品。  
> *   有关专有产品/服务的详细信息，请访问“Azure 市场中的专有产品/服务”页 [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)。  

| 要求 | 详细信息 |  
|:--- |:--- | 
| 计费和计量 | VM 必须支持 BYOL 或即用即付每月计费。 |  
| 与 Azure 兼容的虚拟硬盘 (VHD) | 必须基于 Windows 或 Linux 构建 VM。 <ul> <li>有关创建 Linux VHD 的详细信息，请参阅 [Azure 认可的 Linux 发行版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。</li> <li>有关创建 Windows VHD 的详细信息，请参阅[创建与 Azure 兼容的 VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)。</li> </ul> |  

>[!Note]
>云解决方案提供商 (CSP) 合作伙伴通道参加现已推出。  请参阅[云解决方案提供商](./cloud-solution-providers.md)营销通过 Microsoft CSP 产品/服务的详细信息的合作伙伴渠道。

## <a name="next-steps"></a>后续步骤

如果尚未注册， 

- 请在市场中[注册](https://azuremarketplace.microsoft.com/sell)。

如果已注册并正在创建新套餐或正在使用现有套餐，

- [登录到云合作伙伴门户](https://cloudpartner.azure.com)，创建或完成套餐。
- 有关详细信息，请参阅[虚拟机产品/服务](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer)。
