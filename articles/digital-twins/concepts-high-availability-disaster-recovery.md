---
title: 高可用性和灾难恢复
titleSuffix: Azure Digital Twins
description: 介绍 Azure 和 Azure 数字孪生功能，这些功能可帮助你使用灾难恢复功能构建高可用性 Azure IoT 解决方案。
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 85fd5a4246e891ef6640438b07e12a9c32ad12fa
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094270"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure 数字孪生高可用性和灾难恢复

复原 IoT 解决方案的一个重要方面是业务连续性和灾难恢复。 设计 **高可用性 (HA) ** 和 **灾难恢复 (DR) ** 可帮助你定义和实现解决方案的正常运行时间目标。

本文介绍 Azure 数字孪生服务专门提供的 HA 和 DR 功能。

Azure 数字孪生支持以下功能选项：
* *区域内 HA* –内置冗余，可提供服务运行时间
* *跨区域灾难恢复* –在出现意外的数据中心故障时故障转移到地域配对的 Azure 区域

你还可以查看有关针对 HA/DR 进行设计的一般 Azure 指南的 [*最佳实践*](#best-practices) 部分。

## <a name="intra-region-ha"></a>区域内部 HA
 
Azure 数字孪生通过在服务内实现冗余来提供区域内的 HA。 **Azure 数字孪生解决方案开发人员无需执行其他操作即可利用这些 HA 功能。** 尽管 Azure 数字孪生提供相当高的运行时间保证，但仍会出现暂时性故障，这与任何分布式计算平台相同。 应将相应的重试策略内置于与云应用程序交互的组件，以处理暂时性故障。

## <a name="cross-region-dr"></a>跨区域 DR

在某些极少数情况下，数据中心会因为断电或区域中的其他事件而遇到扩展中断。 此类事件很少见，在这种情况下，以上所述的内部区域 HA 功能可能不会有帮助。 Azure 数字孪生通过 Microsoft 启动的故障转移解决了这种情况。

Microsoft 在少数情况下执行的**故障转移**会将受影响区域的所有 Azure 数字孪生实例故障转移到相应的地理配对区域。 此过程是 (的默认选项，用户无法选择退出) ，无需用户进行干预。 Microsoft 有权决定何时执行此选项。 在用户实例进行故障转移之前，此机制不涉及用户同意。

>[!NOTE]
> 某些 Azure 服务还提供了一个名为 " **客户启动的故障转移**" 的附加选项，使客户只需为其实例启动故障转移，如运行 DR 演练。 Azure 数字孪生目前 **不支持** 此机制。 

## <a name="best-practices"></a>最佳做法

有关 HA/DR 的最佳实践，请参阅本主题中的以下 Azure 指南： 
* [*Azure 业务连续性技术指南*](/azure/architecture/framework/resiliency/overview)一文介绍了可帮助你考虑业务连续性和灾难恢复的通用框架。 
* [*Azure 应用程序的灾难恢复和高可用性*](/azure/architecture/framework/resiliency/backup-and-recovery)一文提供了有关 azure 应用程序的策略指导原则，以实现高可用性 (HA) 和灾难恢复 (DR) 。

## <a name="next-steps"></a>后续步骤 

详细了解 Azure 数字孪生解决方案入门：
 
* [*Azure 数字孪生是什么？*](overview.md)
* [*快速入门：浏览示例方案*](quickstart-adt-explorer.md)