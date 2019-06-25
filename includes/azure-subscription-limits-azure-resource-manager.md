---
title: include 文件
description: include 文件
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 712b70960e09a9c2b0e7a998bc0bddbc28c1e112
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173309"
---
| Resource | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 数 |每个区域 25,000 个<sup>1</sup>。 |每个区域 25,000 个。 |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 核心总数 |每个区域 20 个<sup>1</sup> | 联系支持人员。 |
| VM 系列（例如 Dv2 和 F）、每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的核心数 |每个区域 20 个<sup>1</sup> | 联系支持人员。 |
| [共同管理员](../articles/billing-add-change-azure-subscription-administrator.md)每个订阅 |不受限制。 |不受限制。 |
| 每个订阅在每个区域中的[存储帐户数](../articles/storage/common/storage-quickstart-create-account.md) |250 |250 |
| 每个订阅的[资源组数](../articles/azure-resource-manager/resource-group-overview.md) |980 |980 |
| 每个订阅的[可用性集数](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每个区域 2,000 个。 |每个区域 2,000 个。 |
| Azure 资源管理器 API 请求大小 |4,194,304 字节。 |4,194,304 字节。 |
| 每个订阅的标记数<sup>2</sup> |不受限制。 |不受限制。 |
| 每个订阅的唯一标记计算<sup>2</sup> | 10,000 | 10,000 |
| 每个订阅的[云服务数](../articles/cloud-services/cloud-services-choose-me.md) |不适用<sup>3</sup> |不适用<sup>3</sup> |
| 每个订阅的[地缘组数](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |不适用<sup>3</sup> |不适用<sup>3</sup> |
| [订阅级别部署](../articles/azure-resource-manager/deploy-to-subscription.md)每个位置 | 800<sup>4</sup> | 800 |

<sup>1</sup>默认限制改变按产品类别类型，例如免费试用版和即用即付，和的系列，例如 Dv2、 F 和 g。

<sup>2</sup>每个订阅可以应用无限数量的标记。 每个资源或资源组的标记数仅限 15。 当标记数少于或等于 10,000 时，资源管理器仅返回订阅中[唯一标记名和值的列表](/rest/api/resources/tags)。 即使数目超过 10,000，也仍可按标记查找资源。  

<sup>3</sup>使用 Azure 资源组和资源管理器时不再需要这些功能。

<sup>4</sup>达到 800 部署的限制，如果不再需要历史记录中删除部署。 若要删除订阅级别部署，请使用[删除 AzDeployment](/powershell/module/az.resources/Remove-AzDeployment)或[az 部署删除](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)。

> [!NOTE]
> 虚拟机核心数存在区域总数限制。 区域大小系列（例如 Dv2 和 F）也存在限制。这些限制是单独实施的。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 此订阅可以部署 30 个 A1 VM、30 个 D1 VM，或两者的组合，但总共不能超过 30 个核心。 例如，10 个 A1 VM 和 20 个 D1 VM 就是一种组合。  
> <!-- -->
> 
> 

