---
title: Azure 应用程序解决方案模板提供发布指南 |Azure Marketplace
description: 本文介绍在 Azure 市场中发布解决方案模板的要求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084849"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure 应用程序：解决方案模板提供发布要求

本文介绍解决方案模板产品/服务类型的要求，这是在 Azure Marketplace 中发布 Azure 应用程序产品/服务的一种方法。 解决方案模板产品类型需要[Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/templates/overview.md)才能自动部署解决方案基础结构。

需要以下条件时使用 Azure 应用程序解决方案模板产品类型：

- 解决方案需要在单个 VM 之外进行额外的部署和配置自动化，如 Vm、网络和存储资源的组合。
- 你的客户将自行管理解决方案。

用户为此产品/服务类型看到的操作调用为 "立即获取"。

## <a name="requirements-for-solution-template-offers"></a>解决方案模板提供的要求

| **惠?** | **详细信息**  |
| ---------------  | -----------  |
|计费和计量    |  解决方案模板产品/服务不是提供服务，但可用于部署通过 Microsoft 商业市场计费的付费 VM 产品/服务。 将在客户的 Azure 订阅中预配解决方案的 ARM 模板部署的资源。 即用即付（PAYGO）虚拟机将通过 Microsoft 与客户进行交易，并通过客户的 Azure 订阅计费。<br/> 如果是自带许可 (BYOL)，虽然 Microsoft 将对客户订阅中产生的基础结构成本进行计费，但你会直接向客户支付软件许可费用。   |
|与 Azure 兼容的虚拟硬盘 (VHD)  |   必须基于 Windows 或 Linux 构建 VM。 有关详细信息，请参见: <ul> <li>[创建 Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)（适用于 Windows vhd）</li><li>[Azure 上的 linux 分发](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)版（适用于 linux vhd）。</li></ul> |
| 客户使用情况归因 | 发布到 Azure 市场的所有解决方案模板都需要启用客户使用情况归因。 有关客户使用情况归因及其启用方式的详细信息，请参阅 [Azure 合作伙伴客户使用情况归因](./azure-partner-customer-usage-attribution.md)。  |
| 使用托管磁盘 | [托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是 Azure 中 IaaS vm 的持久磁盘的默认选项。 必须使用解决方案模板中的托管磁盘。 <br> <br> 1. 按照使用 Azure ARM 模板中的托管磁盘的[指导](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)和[示例](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)更新解决方案模板。 <br> <br> 2. 按照以下说明将托管磁盘的基础 VHD 导入到存储帐户，以将 VHD 作为 Marketplace 中的映像发布： <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>后续步骤

- 如果尚未这样做，请[了解](https://azuremarketplace.microsoft.com/sell)Azure Marketplace。
- [登录到合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，创建或完成产品/服务。
- [创建 Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)以获取详细信息。
