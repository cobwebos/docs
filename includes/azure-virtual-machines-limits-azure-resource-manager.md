---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334724"
---
| 资源 | 限制 |
| --- | --- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 数 |每个区域 25,000 个<sup>1</sup>。 |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 核心总数 |每个区域 20<sup>1。</sup> 联系支持以增加限制。 |
| Azure Spot VM 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的总内核数 |每个区域 20<sup>1。</sup> 联系支持以增加限制。 |
| VM 系列（例如 Dv2 和 F）、每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的核心数 |每个区域 20<sup>1。</sup> 联系支持以增加限制。 |
| 每个订阅[的可用性集](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每个区域 2,000 个。 |
| 每个可用性集的虚拟机数 | 200 |
| 每个订阅的证书数 |无限制<sup>2</sup> |

<sup>1</sup>默认限制因产品/服务类别类型而异，例如免费试用和即用即付，以及按系列（如 Dv2、F 和 G）更改。例如，企业协议订阅的默认值为 350。

<sup>2</sup>使用 Azure 资源管理器，证书存储在 Azure 密钥保管库中。 对于每个订阅，证书个数没有限制。 对于每个部署（包括单一 VM 或可用性集），证书的限制为 1-MB。

> [!NOTE]
> 虚拟机核心数存在区域总数限制。 区域大小系列（例如 Dv2 和 F）也存在限制。这些限制是单独实施的。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 此订阅可以部署 30 个 A1 VM、30 个 D1 VM，或两者的组合，但总共不能超过 30 个核心。 例如，10 个 A1 VM 和 20 个 D1 VM 就是一种组合。  
> <!-- -->
> 
