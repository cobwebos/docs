---
title: "Azure 堆栈中的配额类型 |Microsoft 文档"
description: "查看可供服务和 Azure 堆栈中的资源的不同配额类型。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/23/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: da4105fa88848b14e5d5d0289859cfd0f85c8fee
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="quota-types-in-azure-stack"></a>Azure 堆栈中的配额类型

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

[配额](azure-stack-plan-offer-quota-overview.md#plans)定义用户订阅可以设置或使用的资源的限制。 例如，配额可能允许用户创建最多五个 Vm。 每个资源可以有其自身类型的配额。

## <a name="compute-quota-types"></a>计算配额类型
| **类型** | **默认值** | **说明** |
| --- | --- | --- |
| 虚拟机的最大数量 | 20 | 最大订阅可以在此位置创建的虚拟机数。 |
| 虚拟机内核的最大数量 | 50 | 最大可以在此位置创建订阅的内核数 （例如，A3 VM 有四个核心）。 |
| 最大数量的可用性集 | 10 | 可以在此位置中创建的可用性集最大数目。 |
| 虚拟机规模的最大数量设置 | 20 | 可以在此位置中创建的虚拟机规模集的最大数量。 |

> [!NOTE]
> 计算此技术预览版中不强制配额。
> 
> 

## <a name="storage-quota-types"></a>存储配额类型
| **Item** | **默认值** | **说明** |
| --- | --- | --- |
| 最大容量 (GB) |500 |可供此位置中订阅的总存储容量。 |
| 存储帐户的总次数 |20 |最大订阅可以在此位置创建的存储帐户数。 |

## <a name="network-quota-types"></a>网络配额类型
| **Item** | **默认值** | **说明** |
| --- | --- | --- |
| 最大公共 Ip |50 |最大订阅可以在此位置创建的公共 Ip 数。 |
| 最大虚拟网络 |50 |订阅可以在此位置创建的虚拟网络的最大数量。 |
| 最大虚拟网络网关 |1 |虚拟网络网关 （VPN 网关） 订阅可以在此位置中创建最大数量。 |
| 达到最大网络连接 |2 |最大订阅可以创建在此位置中的所有虚拟网络网关之间的网络连接 （点到点或站点到站点） 数。 |
| 最大负载平衡器 |50 |在此位置可以创建订阅的负载平衡器最大数量。 |
| 最大 NIC 数 |100 |订阅可以在此位置创建的网络接口的最大数量。 |
| 最大网络安全组 |50 |在此位置可以创建订阅的网络安全组中最大的数。 |

## <a name="view-an-existing-quota"></a>查看现有的配额
1. 单击**更多的服务** > **资源提供程序**。
2. 选择与你想要查看的配额的服务。
3. 单击**配额**，然后选择你想要查看的配额。

## <a name="next-steps"></a>后续步骤
[了解更多有关计划、 服务和配额。](azure-stack-plan-offer-quota-overview.md)

[创建计划时创建配额。](azure-stack-create-plan.md)
