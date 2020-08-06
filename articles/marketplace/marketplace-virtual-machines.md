---
title: Azure Marketplace 上虚拟机产品/服务的发布指南
description: 本文介绍了发布虚拟机和从 Azure Marketplace 部署的软件免费试用版的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/15/2020
ms.openlocfilehash: 9f408174b9a8dff925a9c834ea75bf725ec6f913
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837338"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>虚拟机产品/服务的发布指南

将虚拟机 (VM) 映像发布是将解决方案发布到 Azure Marketplace 的主要方法之一。 使用本指南来了解此类产品/服务的要求。 

虚拟机产品/服务是通过 Azure Marketplace 部署并计费的事务产品。 用户看到的对操作的调用*现在立即获得*。

## <a name="free-trial"></a>免费试用 

若要安排用户测试您的产品/服务，请在使用 "自带许可证 (BYOL") 计费模式时访问有限条款的软件许可证。 

## <a name="test-drive"></a>体验版

可以通过基础结构即服务 (IaaS) 或软件即服务 (SaaS) 应用来部署一个或多个虚拟机。 "*测试驱动器*发布" 选项的优点是通过合作伙伴托管的指导教程自动设置虚拟机或整个解决方案。 使用测试驱动器，你的客户可以评估 Vm，而无需额外付费。 客户无需成为现有的 Azure 客户便可与试用体验合作。 

有关测试驱动器的详细信息，请参阅[什么是测试驱动器？](what-is-test-drive.md)

|要求  |详细信息 |
|---------|---------|
| 你有 Azure Marketplace 应用   |  通过 IaaS 或 SaaS 部署的一个或多个虚拟机。      |

## <a name="interactive-demo"></a>互动演示

使用此产品/服务，你可以通过交互式演示为客户提供解决方案的引导式体验。 交互式演示发布选项的优点是，你可以提供试用体验，而无需提供复杂解决方案的复杂设置。 

## <a name="virtual-machine-offer"></a>虚拟机套餐

将虚拟设备部署到与客户关联的订阅时，请使用*虚拟机*产品/服务类型。 Vm 完全启用了商务，使用即用即付或自带许可证 (BYOL) 许可模式。 Microsoft 托管商业事务并代你向客户计费。 可获得的好处是能够利用客户与 Microsoft 之间达成的首选付费关系（包括任何企业协议）。

> [!NOTE]
> 目前，与企业协议关联的货币承诺可用于 VM 的 Azure 使用情况，而不是针对你的软件许可费用。  
> 
> [!NOTE]
> 可以通过将映像和定价作为专用产品/服务进行发布，将 VM 的发现和部署限制为一组特定的客户。 专用产品/服务可让你为最接近的客户创建专用产品/服务，并提供自定义的软件和条款。 这些自定义条款可在一系列场景中提供助力，包括使用特殊定价和条款的现场导向型交易以及对限制性版本软件的早期访问。 通过专用产品/服务，您可以通过使用这些详细信息创建新计划，为一组有限的客户提供具体的定价或产品。  
>
> 有关详细信息，请参阅[Azure Marketplace 上的专用产品/服务](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)。  

| 要求 | 详细信息 |  
|:--- |:--- | 
| 计费和计量 | VM 必须支持 BYOL 或即用即付每月计费。 |  
| 与 Azure 兼容的虚拟硬盘 (VHD) | 必须基于 Windows 或 Linux 构建 VM。 有关创建 VHD 的详细信息，请参阅： <ul> <li>适用于 Linux Vhd) 的[Azure (认可 linux 分发](../virtual-machines/linux/endorsed-distros.md)版。</li> <li>为 Windows Vhd) [创建与 Azure 兼容的 VHD](./partner-center-portal/azure-vm-create-offer.md) (。</li> </ul> |  

>[!Note]
>云解决方案提供商 (CSP) 合作伙伴渠道选择加入现已推出。 有关通过 Microsoft CSP 合作伙伴渠道营销产品/服务的详细信息，请参阅[云解决方案提供商](./cloud-solution-providers.md)。

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请了解如何[使用 Azure 市场发展云业务](https://azuremarketplace.microsoft.com/sell)。

若要注册并开始使用合作伙伴中心，请执行以下操作：

- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建或完成产品/服务。
- 有关详细信息，请参阅[创建虚拟机产品/服务](./partner-center-portal/azure-vm-create-offer.md)。
