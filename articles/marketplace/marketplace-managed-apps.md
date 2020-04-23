---
title: Azure 应用程序托管应用程序提供发布指南 |Azure 应用商店
description: 本文介绍在 Azure 应用商店中发布托管应用程序的要求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084866"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure 应用程序：托管应用程序提供发布要求

本文介绍托管应用程序产品/服务类型的要求，这是在 Azure 应用商店中发布 Azure 应用程序产品/服务的一种方式。 托管应用程序是通过 Azure 应用商店部署和计费的代理服务。 用户看到的号召性用语是“立即获取”。

当需要以下条件时，请使用托管应用程序产品/服务类型：

- 您可以使用 VM 或基于 IaaS 的整个解决方案为客户部署基于订阅的解决方案。
- 你或客户要求解决方案由合作伙伴管理。

>[!NOTE]
>例如，合作伙伴可能是 SI 或托管服务提供商 (MSP)。  

## <a name="managed-application-offer"></a>“托管应用程序”产品/服务

|要求 |详细信息  |
|---------|---------|
|部署到客户的 Azure 订阅 | 托管应用必须部署在客户的订阅中，并且可以由第三方管理。 |
|计费和计量    |  资源将在客户的 Azure 订阅中预配。 即用即付 （PAYGO） 虚拟机将通过 Microsoft 与客户进行交易，通过客户的 Azure 订阅 （PAYGO） 计费。 <br> 在自带许可证的情况下，虽然 Microsoft 将向客户订阅中产生的基础结构成本收费，但您将直接向客户支付软件许可费用。        |
|与 Azure 兼容的虚拟硬盘 (VHD)    |   必须基于 Windows 或 Linux 构建 VM。<ul> <ul> <li>有关创建 Linux VHD 的详细信息，请参阅 [Azure 认可的 Linux 发行版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。</li> <li>有关创建 Windows VHD 的详细信息，请参阅[创建 Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)。</li> </ul> |

>[!NOTE]
> 托管应用必须可通过市场部署。 如果客户沟通是个问题，应该在启用商机分享后与感兴趣的客户交流。  

>[!Note]
>云解决方案提供商 （CSP） 合作伙伴渠道选择加入现已可用。 有关通过 Microsoft CSP 合作伙伴渠道营销产品/服务的详细信息，请参阅[云解决方案提供商](./cloud-solution-providers.md)。

## <a name="next-steps"></a>后续步骤

- 如果尚未这样做，[则了解](https://azuremarketplace.microsoft.com/sell)Azure 应用商店。
- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)创建或完成您的优惠。
- [有关详细信息，请创建 Azure 应用程序产品/](./partner-center-portal/create-new-azure-apps-offer.md)服务。
