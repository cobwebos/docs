---
title: Azure 应用程序解决方案模板提供发布指南 |Azure 应用商店
description: 本文介绍在 Azure 市场中发布解决方案模板的要求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084849"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure 应用程序：解决方案模板提供发布要求

本文介绍解决方案模板产品提供类型的要求，这是在 Azure 应用商店中发布 Azure 应用程序产品/服务的一种方式。 解决方案模板提供类型需要[Azure 资源管理器模板 （ARM 模板）](../azure-resource-manager/templates/overview.md)才能自动部署解决方案基础结构。

当需要以下条件时，请使用 Azure 应用程序解决方案模板产品类型：

- 您的解决方案需要单个 VM 以外的其他部署和配置自动化，例如 VM、网络和存储资源的组合。
- 您的客户将自行管理解决方案。

用户为此产品/服务类型看到的操作调用是"立即获取"。

## <a name="requirements-for-solution-template-offers"></a>解决方案模板提供要求

| **要求** | **详细信息**  |
| ---------------  | -----------  |
|计费和计量    |  解决方案模板产品/服务不是交易优惠，但可用于部署通过 Microsoft 商业市场计费的付费 VM 产品/服务。 解决方案的 ARM 模板部署的资源将在客户的 Azure 订阅中预配。 即用即付 （PAYGO） 虚拟机将通过 Microsoft 与客户进行交易，通过客户的 Azure 订阅计费。<br/> 如果是自带许可 (BYOL)，虽然 Microsoft 将对客户订阅中产生的基础结构成本进行计费，但你会直接向客户支付软件许可费用。   |
|与 Azure 兼容的虚拟硬盘 (VHD)  |   必须基于 Windows 或 Linux 构建 VM。 有关详细信息，请参阅： <ul> <li>[创建 Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)（适用于 Windows VHD）</li><li>[在 Azure 上认可的 Linux 发行版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)（对于 Linux VHD）。</li></ul> |
| 客户使用情况归因 | 发布到 Azure 市场的所有解决方案模板都需要启用客户使用情况归因。 有关客户使用情况归因及其启用方式的详细信息，请参阅 [Azure 合作伙伴客户使用情况归因](./azure-partner-customer-usage-attribution.md)。  |
| 使用托管磁盘 | [托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是 Azure 中 IaaS VM 的持久磁盘的默认选项。 必须在解决方案模板中使用托管磁盘。 <br> <br> 1. 按照 Azure ARM 模板中使用托管磁盘[的指南](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)和[示例](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)更新解决方案模板。 <br> <br> 2. 按照以下说明将托管磁盘的基础 VHD 导入存储帐户，以在应用商店中将 VHD 作为映像发布： <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>后续步骤

- 如果尚未这样做，[则了解](https://azuremarketplace.microsoft.com/sell)Azure 应用商店。
- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)创建或完成您的优惠。
- [有关详细信息，请创建 Azure 应用程序产品/](./partner-center-portal/create-new-azure-apps-offer.md)服务。
