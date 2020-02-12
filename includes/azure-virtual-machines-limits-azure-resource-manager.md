---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 8607fd6f01ea02841017d09dac7236812f2330b9
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133704"
---
| 资源 | 默认限制 |
| --- | --- |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 数 |每个区域 25000<sup>1</sup> 。 |
| 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的 VM 核心总数 |每个区域20个<sup>1</sup> 。 联系支持人员以增加限制。 |
| Azure 点 VM 每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的总内核数 |每个区域20个<sup>1</sup> 。 联系支持人员以增加限制。 |
| VM 每个系列，如 Dv2 和 F，每个[订阅](../articles/billing-buy-sign-up-azure-subscription.md)的核心数 |每个区域20个<sup>1</sup> 。 联系支持人员以增加限制。 |
| 每个订阅的[可用性集](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每个区域2000。 |
| 每个可用性集的虚拟机数 | 200 |
| 每个订阅的证书数 |无限制<sup>2</sup> |

<sup>1</sup>默认限制根据产品类别类型（例如免费试用版和即用即付，以及按序列（如 Dv2、F 和 G）而有所不同。例如，企业协议订阅的默认值为350。

<sup>2</sup>在 Azure 资源管理器中，证书存储在 Azure Key Vault 中。 订阅的证书数不受限制。 每个部署有 1 MB 的证书限制，其中包括单个 VM 或可用性集。

> [!NOTE]
> 虚拟机核心具有区域总数限制。 它们还限制了区域的每个大小的系列，如 Dv2 和 F。这些限制是单独强制执行的。 例如，某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 此订阅可以部署30个 A1 Vm 或30个 D1 Vm，或者两者的组合（不超过30个核心）。 例如，10个 A1 Vm 和20个 D1 Vm 的组合的示例。  
> <!-- -->
> 
