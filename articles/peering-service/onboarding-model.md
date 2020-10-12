---
title: Azure 对等互连服务载入模型
description: 了解如何加入 Azure 对等互连服务
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871397"
---
# <a name="onboarding-peering-service-model"></a>载入对等互连服务模型

对等互连服务的载入过程由如下所列的两个模型组成：

 - 载入对等互连服务连接

 - 载入对等互连服务连接遥测

上面列出的模型的操作计划如下所示：

| **步骤** | **操作**| **提供的服务**|
|-----------|---------|---------|---------|
|1|客户从连接伙伴预配连接 (不与 Microsoft) 交互。 |与 microsoft 建立良好连接的 Internet 提供商，并满足与 Microsoft 的高性能和可靠连接性的技术要求。  |
|2 (可选) |客户将位置注册到 Azure 门户。 位置由以下内容定义： ISP/IXP 名称、客户站点的物理位置 (状态级别) 、服务提供商或企业提供给该位置的 IP 前缀。  |遥测： Internet 路由监视、从 Microsoft 到用户最接近边缘 POP 位置的流量优先级。 |



## <a name="onboarding-peering-service-connection"></a>载入对等互连服务连接

若要加入对等互连服务连接，请执行以下操作：

与 Internet 服务提供商 (ISP) 或 Internet 交换 (IX) 合作伙伴合作，以获取对等互连服务，将网络连接到 Microsoft 网络。

确保 [连接服务提供商](location-partners.md) 与 Microsoft 合作提供服务。 

## <a name="onboarding-peering-service-connection-telemetry"></a>载入对等互连服务连接遥测

客户可以选择其遥测数据（如 BGP 路由分析）来监视网络延迟和访问 Microsoft 网络时的性能。 这可以通过将连接注册到 Azure 门户来实现。

若要加入对等互连服务连接遥测，客户必须将对等互连服务连接注册到 Azure 门户中。 请参阅 [注册对等互连服务-Azure 门户](azure-portal.md) 来了解该过程。

之后，可以通过 [在此处](measure-connection-telemetry.md)引用来度量遥测数据。

## <a name="next-steps"></a>后续步骤

若要了解如何注册对等互连服务连接的分步过程，请参阅 [注册对等互连服务-Azure 门户](azure-portal.md)。

若要了解如何度量连接遥测，请参阅 [度量连接遥测](measure-connection-telemetry.md)。
