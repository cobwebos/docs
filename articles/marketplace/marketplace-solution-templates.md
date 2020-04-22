---
title: Azure 应用程序解决方案模板提供发布指南 |Azure 应用商店
description: 本文介绍在 Azure 市场中发布解决方案模板的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: faff12adbf6c0f88f3161e1e4f2760da3b7966ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687514"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure 应用程序：解决方案模板产品/服务发布指南

解决方案模板是在市场中发布解决方案的主要方式之一。 使用本指南了解此产品/服务的要求。 

如果解决方案需要在单个 VM 以外实现附加的部署和配置自动化，请使用“Azure 应用：解决方案模板”产品/服务类型。 可以使用“Azure 应用：解决方案模板”自动预配一个或多个 VM。 还可以预配网络和存储资源。 “Azure 应用：解决方案模板”产品/服务类型为单个 VM 和整个基于 IaaS 的解决方案提供自动化优势。

这些解决方案模板不是事务报价，但可用于部署通过 Microsoft 商业市场计费的付费 VM 产品/服务。 用户看到的号召性用语是“立即获取”。


## <a name="requirements-for-solution-templates"></a>解决方案模板的要求

| **要求** | **详细信息**  |
| ---------------  | -----------  |
|计费和计量    |  资源将在客户的 Azure 订阅中预配。 即用即付 （PAYGO） 虚拟机将通过 Microsoft 与客户进行交易，通过客户的 Azure 订阅 （PAYGO） 计费。  <br/> 如果是自带许可 (BYOL)，虽然 Microsoft 将对客户订阅中产生的基础结构成本进行计费，但你会直接向客户支付软件许可费用。   |
|与 Azure 兼容的虚拟硬盘 (VHD)  |   必须基于 Windows 或 Linux 构建 VM。  有关详细信息，请参阅创建[Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)。 |
| 客户使用情况归因 | 发布到 Azure 市场的所有解决方案模板都需要启用客户使用情况归因。 有关客户使用情况归因及其启用方式的详细信息，请参阅 [Azure 合作伙伴客户使用情况归因](./azure-partner-customer-usage-attribution.md)。  |
| 使用托管磁盘 | [托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是 Azure 中 IaaS VM 的持久磁盘的默认选项。 必须在解决方案模板中使用托管磁盘。 <br> <br> 1. 按照 Azure ARM 模板中使用托管磁盘[的指南](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)和[示例](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)更新解决方案模板。 <br> <br> 2. 按照以下说明将托管磁盘的基础 VHD 导入存储帐户，以在应用商店中将 VHD 作为映像发布： <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>后续步骤

如果尚未注册， 

- [了解](https://azuremarketplace.microsoft.com/sell)市场。

要在合作伙伴中心注册，请开始创建新产品/服务或处理现有产品/服务：

- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)创建或完成您的优惠。
- 有关详细信息[，请参阅创建 Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)。

