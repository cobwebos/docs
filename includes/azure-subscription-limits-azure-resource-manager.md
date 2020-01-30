---
title: include 文件
description: include 文件
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d94937a738034904413eac8b256121f14221d1ac
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845955"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 数 |每个区域 25000<sup>1</sup> 。 |每个区域25000。 |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 核心总数 |每个区域20个<sup>1</sup> 。 | 联系支持人员。 |
| Azure 点 VM 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的总内核数 |每个区域20个<sup>1</sup> 。 | 联系支持人员。 |
| VM 每个系列，如 Dv2 和 F，每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的核心数 |每个区域20个<sup>1</sup> 。 | 联系支持人员。 |
| 每个订阅在每个区域中的[存储帐户数](../articles/storage/common/storage-account-create.md) |250 |250 |
| 每个订阅的[可用性集](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每个区域2000。 |每个区域2000。 |
| 每个订阅的[地缘组数](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |不适用<sup>3</sup> |不适用<sup>3</sup> |
| 每个订阅的[云服务数](../articles/cloud-services/cloud-services-choose-me.md) |不适用<sup>3</sup> |不适用<sup>3</sup> |
| 每个订阅的[资源组](../articles/azure-resource-manager/management/overview.md) |980 |980 |
| Azure 资源管理器 API 请求大小 |4194304字节。 |4194304字节。 |
| 每个订阅的标记<sup>2</sup> |不受限制。 |不受限制。 |
| 每个订阅的唯一标记计算<sup>2</sup> | 10,000 | 10,000 |
| 每个位置[的订阅级别部署](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>4</sup> | 800 |
| 每 Azure Active Directory 租户的订阅 | 不受限制。 | 不受限制。 |
| 每个订阅的[共同](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |不受限制。 |不受限制。 |

<sup>1</sup>默认限制根据产品类别类型（例如免费试用版和即用即付，以及按序列（如 Dv2、F 和 G）而有所不同。例如，企业协议订阅的默认值为350。

<sup>2</sup>每个订阅可以应用无限数量的标记。 每个资源或资源组的标记数限制为50。 仅当标记数为10000或更低时，资源管理器才返回订阅中[唯一标记名称和值的列表](/rest/api/resources/tags)。 当数字超过10000时，仍可以按标记查找资源。  

<sup>3</sup>Azure 资源组和资源管理器不再需要这些功能。

<sup>4</sup>如果达到了800个部署的限制，请从不再需要的历史记录中删除部署。 若要删除订阅级别部署，请使用[AzDeployment](/powershell/module/az.resources/Remove-AzDeployment)或[az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)。

> [!NOTE]
> 虚拟机核心具有区域总数限制。 它们还限制了区域的每个大小的系列，如 Dv2 和 F。这些限制是单独强制执行的。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 此订阅可以部署30个 A1 Vm 或30个 D1 Vm，或者两者的组合（不超过30个核心）。 例如，10个 A1 Vm 和20个 D1 Vm 的组合的示例。  
> <!-- -->
> 
> 

