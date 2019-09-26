---
title: Azure 应用程序解决方案模板提供发布指南 |Azure Marketplace
description: 本文介绍在 Azure 市场中发布解决方案模板的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 9/25/2019
ms.author: ellacroi
ms.openlocfilehash: 725be2ee239a879be8200d33acaf566b1d42d446
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300342"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure 应用程序：解决方案模板产品/服务发布指南

解决方案模板是在市场中发布解决方案的主要方式之一。 使用本指南了解此产品/服务的要求。 

如果解决方案需要在单个 VM 以外实现附加的部署和配置自动化，请使用“Azure 应用：解决方案模板”产品/服务类型。 可以使用“Azure 应用：解决方案模板”自动预配一个或多个 VM。 还可以预配网络和存储资源。 “Azure 应用：解决方案模板”产品/服务类型为单个 VM 和整个基于 IaaS 的解决方案提供自动化优势。

这些解决方案模板不是事务提供的，但可用于部署通过 Microsoft 的商业市场计费的付费 VM 优惠。 用户看到的号召性用语是“立即获取”。


## <a name="requirements-for-solution-templates"></a>解决方案模板的要求

| **要求** | **详细信息**  |
| ---------------  | -----------  |
|计费和计量    |  资源将在客户的 Azure 订阅中预配。 即用即付 (PAYGO) 虚拟机将通过 Microsoft 与客户进行交易，通过客户的 Azure 订阅 (PAYGO) 进行计费。  <br/> 如果是自带许可 (BYOL)，虽然 Microsoft 将对客户订阅中产生的基础结构成本进行计费，但你会直接向客户支付软件许可费用。   |
|与 Azure 兼容的虚拟硬盘 (VHD)  |   必须基于 Windows 或 Linux 构建 VM。  有关详细信息，[请参阅创建与 Azure 兼容的 VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)。 |
| 客户使用情况归因 | 发布到 Azure 市场的所有解决方案模板都需要启用客户使用情况归因。 有关客户使用情况归因及其启用方式的详细信息，请参阅 [Azure 合作伙伴客户使用情况归因](./azure-partner-customer-usage-attribution.md)。  |
| 使用托管磁盘 | [托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是 Azure 中 IaaS vm 的持久磁盘的默认选项。 必须使用解决方案模板中的托管磁盘。 <br> <br> 1.按照使用 Azure ARM 模板中的托管磁盘的[指导](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)和[示例](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)更新解决方案模板。 <br> <br> 2.按照以下说明将托管磁盘的基础 VHD 导入到存储帐户，以将 VHD 作为 Marketplace 中的映像发布： <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>后续步骤
如果尚未注册，请在市场中[注册](https://azuremarketplace.microsoft.com/sell)。

如果已注册并正在创建新产品/服务或正在使用现有产品/服务，请登录到[云合作伙伴门户](https://cloudpartner.azure.com)创建或完成产品/服务。
