---
title: Azure 对等互连服务连接
description: 了解 Microsoft Azure 对等互连服务连接
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398914"
---
# <a name="peering-service-connection"></a>对等连接服务连接

连接通常是指确定对等互连服务的逻辑信息集。 它通过指定以下属性进行定义：

- 逻辑名称
- 连接伙伴
- 客户的物理位置
- IP 前缀

客户可以根据要求建立单个连接或多个连接。 连接也用作遥测收集的单位。 例如，若要选择遥测警报，客户必须定义将监视的连接。

> [!Note]
> 注册对等互连服务时，我们会分析你的 Windows 和 Microsoft 365 遥测数据，以便为你提供所选前缀的延迟度量。
>有关连接遥测的详细信息，请参阅对 [等互连服务连接遥测](connection-telemetry.md)。
>

>## <a name="how-to-register-a-connection"></a>如何注册连接？

**方案** -假设分支机构分布在不同的地理位置，如下图所示。 在此，客户需要提供逻辑名称、服务提供商 (SP) 名称、客户的物理位置和 IP 前缀，这些前缀 (客户拥有或由服务提供商分配，) 与单个连接关联。 必须重复此过程以将对等互连服务注册为单独的异地冗余连接。

![异地冗余连接](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> 状态级别-当连接位于美国中时，会将筛选视为客户的物理位置。
>

## <a name="next-steps"></a>后续步骤

若要了解如何注册对等互连服务连接的分步过程，请参阅 [使用 Azure 门户注册对等互连服务](azure-portal.md)。

若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。

若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
