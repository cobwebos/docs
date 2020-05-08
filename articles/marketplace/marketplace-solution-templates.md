---
title: Azure 应用程序的发布指南解决方案模板产品/服务-Azure Marketplace
description: 本文介绍了在 Azure Marketplace 中发布解决方案模板的要求。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: f62b3478c5c711423913b5918886b43b79ac691d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858322"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Azure 应用程序的发布指南解决方案模板产品

本文介绍发布解决方案模板产品/服务的要求，这是在 Azure Marketplace 中发布 Azure 应用程序产品/服务的一种方法。 解决方案模板产品类型需要[Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/templates/overview.md)才能自动部署解决方案基础结构。

在以下条件下使用 Azure 应用程序*解决方案模板*产品/服务类型：

- 你的解决方案需要在单个虚拟机（VM）（例如 Vm、网络和存储资源的组合）之外实现额外的部署和配置自动化。
- 你的客户将自行管理解决方案。

客户查看此产品/服务类型的操作的调用*现在立即获得*。

## <a name="requirements-for-solution-template-offers"></a>解决方案模板提供的要求

| **要求** | **详细信息**  |
| ---------------  | -----------  |
|计费和计量    |  解决方案模板产品/服务不是事务提供，但可用于部署通过 Microsoft 商业市场计费的付费 VM 产品/服务。 解决方案的 ARM 模板部署的资源是在客户的 Azure 订阅中设置的。 即用即付虚拟机通过 Microsoft 与客户进行交易，并通过客户的 Azure 订阅进行计费。<br/> 对于自带许可证（BYOL）计费，虽然 Microsoft 会对客户订阅中的基础结构成本收费，但你可以直接向客户收取软件许可费用。   |
|与 Azure 兼容的虚拟硬盘 (VHD)  |   必须基于 Windows 或 Linux 构建 VM。 有关详细信息，请参见: <ul> <li>[创建 Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)（适用于 Windows vhd）。</li><li>[Azure 上的 linux 分发](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)版（适用于 linux vhd）。</li></ul> |
| 客户使用情况归因 | 在 Azure Marketplace 中发布的所有解决方案模板上都需要启用客户使用情况归属。 有关客户使用情况和如何启用的详细信息，请参阅[Azure 合作伙伴客户使用归属](./azure-partner-customer-usage-attribution.md)。  |
| 使用托管磁盘 | 对于 Azure 中的基础结构即服务（IaaS） Vm，[托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是默认选项。 必须使用解决方案模板中的托管磁盘。 <ul><li>若要更新解决方案模板，请遵循在[Azure 中使用托管磁盘资源管理器模板](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)中的指南，并使用提供的[示例](https://github.com/Azure/azure-quickstart-templates)。<br><br> </li><li>若要在 Azure Marketplace 中将 VHD 作为映像发布，请使用以下方法之一将托管磁盘的基础 VHD 导入到存储帐户：<ul><li>[Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [Azure CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul></ul> |

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请了解如何[通过 Azure Marketplace 拓展云业务](https://azuremarketplace.microsoft.com/sell)。

若要注册并开始使用合作伙伴中心，请执行以下操作：

- [登录到合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，创建或完成产品/服务。
- 有关详细信息，请参阅[创建 Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)。
