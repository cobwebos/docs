---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 651027ffd63a376ff0b8595636ece4c8f39c86e8
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82616099"
---
| 资源 | 限制 |
| --- | --- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 数 |每个区域 25,000 个<sup>1</sup>。 |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 核心总数 |每个区域20个<sup>1</sup> 。 联系支持人员以增加限制。 |
| Azure 点 VM 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的总内核数 |每个区域20个<sup>1</sup> 。 联系支持人员以增加限制。 |
| VM 系列（例如 Dv2 和 F）、每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的核心数 |每个区域20个<sup>1</sup> 。 联系支持人员以增加限制。 |
| 每个订阅的[可用性集](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每个区域2500。 |
| 每个可用性集的虚拟机数 | 200 |
| 每个订阅的证书数 |无限制<sup>2</sup> |

<sup>1</sup>默认限制根据产品类别类型（例如免费试用版和即用即付，以及按序列（如 Dv2、F 和 G）而有所不同。例如，企业协议订阅的默认值为350。

<sup>2</sup>在 Azure 资源管理器中，证书存储在 Azure Key Vault 中。 对于每个订阅，证书个数没有限制。 对于每个部署（包括单一 VM 或可用性集），证书的限制为 1-MB。

> [!NOTE]
> 虚拟机核心数存在区域总数限制。 区域大小系列（例如 Dv2 和 F）也存在限制。这些限制是单独实施的。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 此订阅可以部署 30 个 A1 VM、30 个 D1 VM，或两者的组合，但总共不能超过 30 个核心。 例如，10 个 A1 VM 和 20 个 D1 VM 就是一种组合。  
> <!-- -->
> 
