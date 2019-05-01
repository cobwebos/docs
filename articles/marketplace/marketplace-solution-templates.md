---
title: Azure 应用程序解决方案模板产品/服务发布指南
description: 本文介绍在 Azure 市场中发布解决方案模板的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: bbf9ab56bd7e070e40e2454a69f0a5e313597f14
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765493"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure 应用程序：解决方案模板产品/服务发布指南

解决方案模板是在市场中发布解决方案的主要方式之一。 使用本指南了解此产品/服务的要求。 

如果解决方案需要在单个 VM 以外实现附加的部署和配置自动化，请使用“Azure 应用：解决方案模板”产品/服务类型。 可以使用“Azure 应用：解决方案模板”自动预配一个或多个 VM。 还可以预配网络和存储资源。 “Azure 应用：解决方案模板”产品/服务类型为单个 VM 和整个基于 IaaS 的解决方案提供自动化优势。

这些解决方案模板是通过市场部署和计费的事务产品/服务。 用户看到的号召性用语是“立即获取”。


## <a name="requirements-for-solution-templates"></a>解决方案模板的要求

| **要求** | **详细信息**  |
| ---------------  | -----------  |
|计费和计量    |  资源将在客户的 Azure 订阅中预配。 即用即付 (PAYGO) 虚拟机将通过 Microsoft 与客户进行交易，通过客户的 Azure 订阅 (PAYGO) 进行计费。  <br/> 如果是自带许可 (BYOL)，虽然 Microsoft 将对客户订阅中产生的基础结构成本进行计费，但你会直接向客户支付软件许可费用。   |
|与 Azure 兼容的虚拟硬盘 (VHD)  |   必须基于 Windows 或 Linux 构建 VM。  有关详细信息，[请参阅创建与 Azure 兼容的 VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)。 |
| 客户使用情况归因 | 发布到 Azure 市场的所有解决方案模板都需要启用客户使用情况归因。 有关客户使用情况归因及其启用方式的详细信息，请参阅 [Azure 合作伙伴客户使用情况归因](./azure-partner-customer-usage-attribution.md)。  |
|  |  |

## <a name="next-steps"></a>后续步骤
如果尚未注册，请在市场中[注册](https://azuremarketplace.microsoft.com/sell)。

如果已注册并正在创建新产品/服务或正在使用现有产品/服务，请登录到[云合作伙伴门户](https://cloudpartner.azure.com)创建或完成产品/服务。
