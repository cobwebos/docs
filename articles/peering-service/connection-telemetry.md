---
title: Azure 对等互连服务连接遥测
description: 了解 Microsoft Azure 对等互连服务连接遥测
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871297"
---
# <a name="peering-service-connection-telemetry"></a>对等互连服务连接遥测

连接遥测数据就客户位置与 Microsoft 网络之间的连接收集了相关见解。 客户可以通过在 Azure 门户中注册连接来获取 Azure 对等互连服务连接的遥测数据。 此功能提供了前缀安全和网络性能的见解。


连接遥测数据由以下作用域组成：

### <a name="latency-measurement"></a>延迟度量

 对于对等互连服务中已注册的前缀，延迟是从客户端到 Microsoft 边缘 PoP 的度量。

### <a name="route-prefix-monitoring-and-protection"></a>路由前缀监视和保护

监视路由路径中的任何可疑活动，并将其捕获到事件日志中。 例如，为某些因素创建事件日志：

- 前缀劫持
- 前缀提款
- 路由泄漏

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接，请参阅[对等互连服务连接](connection.md)。
- 若要加入对等互连服务连接，请参阅 [加入对等互连服务模型](onboarding-model.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
