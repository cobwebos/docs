---
title: Azure 应用程序“托管应用程序”产品/服务发布指南
description: 本文介绍在市场中发布托管应用程序的要求
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
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 674e91a7c1de026a26cf9a3bf1eaead7f5e86144
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059189"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure 应用程序：“托管应用程序”产品/服务发布指南

解决方案模板是在市场中发布解决方案的主要方式之一。 使用本指南了解此产品/服务的要求。 

以下是通过市场部署和计费的事务产品/服务。 用户看到的号召性用语是“立即获取”。

需要满足以下条件时，请使用“Azure 应用：托管应用”产品/服务类型：
- 使用 VM 为客户部署基于订阅的解决方案，或部署基于 IaaS 的整个解决方案。
- 你或客户要求解决方案由合作伙伴管理。

>[!NOTE]
>例如，合作伙伴可能是 SI 或托管服务提供商 (MSP)。  

## <a name="managed-application-offer"></a>“托管应用程序”产品/服务

|要求 |详细信息  |
|---------|---------|
|部署到客户的 Azure 订阅 | 托管应用必须部署在客户的订阅中，并且可以由第三方管理 | 
|计费和计量    |  资源将在客户的 Azure 订阅中预配。 即用即付 (PAYGO) 虚拟机将通过 Microsoft 与客户进行交易，通过客户的 Azure 订阅 (PAYGO) 进行计费 
如果是自带许可，虽然 Microsoft 将对客户订阅中产生的基础结构成本进行计费，但你会直接向客户支付软件许可费用        |
|与 Azure 兼容的虚拟硬盘 (VHD)    |   必须基于 Windows 或 Linux 构建 VM。<ul> <li>有关创建 Linux VHD 的详细信息，请访问 [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based) 上的“创建与 Azure 兼容的 VHD（基于 Linux）”部分。</li> <li>有关创建 Windows VHD 的详细信息，请访问 [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based) 上的“创建与 Azure 兼容的 VHD（基于 Windows）”部分。</li> </ul>      |

>[!NOTE]
> 托管应用必须可通过市场部署。 如果客户沟通是个问题，应该在启用商机分享后与感兴趣的客户交流。  


## <a name="next-steps"></a>后续步骤
如果尚未注册， 

- 请在市场中[注册](https://azuremarketplace.microsoft.com/sell)

如果已注册并正在创建新套餐或正在使用现有套餐，

- [登录到云合作伙伴门户](https://cloudpartner.azure.com)创建或完成套餐
