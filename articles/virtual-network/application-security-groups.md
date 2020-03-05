---
title: Azure 应用程序安全组概述
titlesuffix: Azure Virtual Network
description: 了解应用程序安全组的使用情况。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 775ef92a0ca486d1f8a6c44c78a4df04cd5ef467
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274705"
---
# <a name="application-security-groups"></a>应用程序安全组

使用应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，从而可以基于这些组将虚拟机分组以及定义网络安全策略。 可以大量重复使用安全策略，而无需手动维护显式 IP 地址。 平台会处理显式 IP 地址和多个规则集存在的复杂性，让你专注于业务逻辑。 若要更好地理解应用程序安全组，请考虑以下示例：

![应用程序安全组](./media/security-groups/application-security-groups.png)

在上图中，*NIC1* 和 *NIC2* 是 *AsgWeb* 应用程序安全组的成员。 *NIC3* 是 *AsgLogic* 应用程序安全组的成员。 *NIC4* 是 *AsgDb* 应用程序安全组的成员。 虽然此示例中的每个网络接口只是一个应用程序安全组的成员，但实际上一个网络接口可以是多个应用程序安全组的成员，具体取决于 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 这些网络接口都没有关联的网络安全组。 *NSG1* 关联到两个子网，包含以下规则：

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

若要让流量从 Internet 流到 Web 服务器，此规则是必需的。 由于来自 Internet 的入站流量被 **DenyAllInbound** 默认安全规则拒绝，因此 *AsgLogic* 或 *AsgDb* 应用程序安全组不需更多规则。

|优先度|源|源端口| 目标 | 目标端口 | 协议 | 访问 |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

## <a name="deny-database-all"></a>Deny-Database-All

由于 **AllowVNetInBound** 默认安全规则允许在同一虚拟网络中的资源之间进行的所有通信，因此需要使用此规则来拒绝来自所有资源的流量。

|优先度|源|源端口| 目标 | 目标端口 | 协议 | 访问 |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Any | 拒绝 |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

此规则允许从 *AsgLogic* 应用程序安全组到 *AsgDb* 应用程序安全组的流量。 此规则的优先级高于 *Deny-Database-All* 规则的优先级。 因此，此规则在 *Deny-Database-All* 规则之前处理，这样系统就会允许来自 *AsgLogic* 应用程序安全组的流量，而阻止所有其他流量。

|优先度|源|源端口| 目标 | 目标端口 | 协议 | 访问 |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

将应用程序安全组指定为源或目标的规则只会应用到属于应用程序安全组成员的网络接口。 如果网络接口不是应用程序安全组的成员，则规则不会应用到网络接口，即使网络安全组关联到子网。

应用程序安全组具有以下约束：

-    一个订阅中可以有的应用程序安全组存在数量限制，此外还有其他与应用程序安全组相关的限制。 有关详细信息，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 可将一个应用程序安全组指定为安全规则中的源和目标。 不能在源或目标中指定多个应用程序安全组。
- 分配给应用程序安全组的所有网络接口都必须存在于分配给应用程序安全组的第一个网络接口所在的虚拟网络中。 例如，如果分配给名为 *AsgWeb* 的应用程序安全组的第一个网络接口位于名为 *VNet1* 的虚拟网络中，则分配给 *ASGWeb* 的所有后续网络接口都必须存在于 *VNet1* 中。 不能向同一应用程序安全组添加来自不同虚拟网络的网络接口。
- 如果在安全规则中将应用程序安全组指定为源和目标，则两个应用程序安全组中的网络接口必须存在于同一虚拟网络中。 例如，如果 *AsgLogic* 包含来自 *VNet1* 的网络接口，*AsgDb* 包含来自 *VNet2* 的网络接口，则不能在一项规则中将 *AsgLogic* 分配为源，将 *AsgDb* 分配为目标。 源和目标应用程序安全组中的所有网络接口需存在于同一虚拟网络中。

> [!TIP]
> 为了尽量减少所需的安全规则数和需要更改规则的情况，请尽可能使用服务标记或应用程序安全组来规划所需的应用程序安全组并创建规则，而不要使用单个 IP 地址或 IP 地址范围。

## <a name="next-steps"></a>后续步骤

* 连接如何[创建网络安全组](tutorial-filter-network-traffic.md)。
