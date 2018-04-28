---
title: Azure Stack 中的配额类型 | Microsoft Docs
description: 查看可用于 Azure Stack 中的服务和资源的不同配额类型。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: b68a963dae4b3621bfd9ecdcbc20146d7b20c457
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="quota-types-in-azure-stack"></a>Azure Stack 中的配额类型

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[配额](azure-stack-plan-offer-quota-overview.md#plans)定义用户订阅可以预配或使用的资源限制。 例如，配额可能允许用户最多创建五个 VM。 每个资源都可以有自已的配额类型。

## <a name="compute-quota-types"></a>计算配额类型
| **类型** | **默认值** | **说明** |
| --- | --- | --- |
| 虚拟机的数目上限 | 20 | 订阅可以在此位置创建的虚拟机数目上限。 |
| 虚拟机核心的数目上限 | 50 | 订阅可以在此位置创建的核心数目上限（例如，A3 VM 有四个核心）。 |
| 可用性集的数目上限 | 10 | 可以在此位置创建的可用性集数目上限。 |
| 虚拟机规模集的数目上限 | 20 | 可以在此位置创建的虚拟机规模集数目上限。 |



## <a name="storage-quota-types"></a>存储配额类型
| **Item** | **默认值** | **说明** |
| --- | --- | --- |
| 最大容量 (GB) |500 |可供此位置的订阅使用的总存储容量。 |
| 存储帐户的总数 |20 |订阅可以在此位置创建的存储帐户数目上限。 |

> [!NOTE]  
> 可能需要最多两个小时，才能强制执行新的存储配额。 
> 
> 

## <a name="network-quota-types"></a>网络配额类型
| **Item** | **默认值** | **说明** |
| --- | --- | --- |
| 公共 IP 的数目上限 |50 |订阅可以在此位置创建的公共 IP 数目上限。 |
| 虚拟网络的数目上限 |50 |订阅可以在此位置创建的虚拟网络数目上限。 |
| 虚拟网络网关的数目上限 |1 |订阅可以在此位置创建的虚拟网络网关（VPN 网关）数目上限。 |
| 网络连接的数目上限 |2 |订阅可以在此位置跨所有虚拟网络网关创建的网络连接（点到点或站点到站点）数目上限。 |
| 负载均衡器的数目上限 |50 |订阅可以在此位置创建的负载均衡器数目上限。 |
| 最大 NIC 数 |100 |订阅可以在此位置创建的网络接口数目上限。 |
| 网络安全组的数目上限 |50 |订阅可以在此位置创建的网络安全组数目上限。 |

## <a name="view-an-existing-quota"></a>查看现有配额
1. 单击“更多服务” > “资源提供程序”。
2. 选择具有要查看的配额的服务。
3. 单击“配额”，然后选择要查看的配额。

## <a name="next-steps"></a>后续步骤
[详细了解计划、产品/服务和配额。](azure-stack-plan-offer-quota-overview.md)

[创建计划时创建配额。](azure-stack-create-plan.md)
