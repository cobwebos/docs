---
title: include 文件
description: include 文件
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a1645a8471f75f1d56f3e61c0adfc3fadee14560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553310"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 数 |25000<sup>1</sup>每个区域。 |每个地区的 25,000。 |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 核心总数 |20<sup>1</sup>每个区域。 | 联系支持人员。 |
| VM 系列，例如 Dv2 和 F，每个核心每[订阅](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup>每个区域。 | 联系支持人员。 |
| [共同管理员](../articles/billing-add-change-azure-subscription-administrator.md)每个订阅 |不受限制。 |不受限制。 |
| 每个订阅在每个区域中的[存储帐户数](../articles/storage/common/storage-quickstart-create-account.md) |200 |200<sup>2</sup> |
| [资源组](../articles/azure-resource-manager/resource-group-overview.md)每个订阅 |980 |980 |
| [可用性集](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)每个订阅 |每个区域 2,000。 |每个区域 2,000。 |
| Azure 资源管理器 API 请求大小 |4,194,304 的字节数。 |4,194,304 的字节数。 |
| 每个订阅的标记数<sup>3</sup> |不受限制。 |不受限制。 |
| 每个订阅的唯一标记计算<sup>3</sup> | 10,000 | 10,000 |
| 每个订阅的[云服务数](../articles/cloud-services/cloud-services-choose-me.md) |N/A<sup>4</sup> |N/A<sup>4</sup> |
| 每个订阅的[地缘组数](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |N/A<sup>4</sup> |N/A<sup>4</sup> |
| [订阅级别部署](../articles/azure-resource-manager/deploy-to-subscription.md)每个位置 | 800 | 800 |

<sup>1</sup>默认限制改变按产品类别类型，例如免费试用版和即用即付，和的系列，例如 Dv2、 F 和 g。

<sup>2</sup>是包含两个标准和高级存储帐户。 如果你需要超过 200 个存储帐户，请通过请求[Azure 支持](https://azure.microsoft.com/support/faq/)。 Azure 存储团队评审你的业务案例，并可以批准最多 250 的存储帐户。

<sup>3</sup>每个订阅可以应用无限数量的标记。 每个资源或资源组的标记数仅限 15。 资源管理器返回[唯一的标记名称和值的列表](/rest/api/resources/tags)仅当标记数为 10000 或更少的订阅中。 您仍可以找到的资源的标记数超过 10,000 时。  

<sup>4</sup>这些功能不再需要使用 Azure 资源组和资源管理器。

> [!NOTE]
> 虚拟机核心数的区域的总限制。 它们还具有限制的区域的每个大小系列，例如 Dv2 和 f。单独强制实施这些限制。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 此订阅可以部署 30 个 A1 Vm，或 30 个 D1 Vm 或两者的组合，不能超过 30 个核心总数。 组合的一个示例是 10 个 A1 Vm 和 20 个 D1 Vm。  
> <!-- -->
> 
> 

