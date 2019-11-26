---
title: Azure 负载均衡器的安全控制
description: 用于评估负载均衡器的安全控制清单
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214900"
---
# <a name="security-controls-for-azure-load-balancer"></a>Azure 负载均衡器的安全控制

本文介绍了内置于 Azure 负载均衡器中的安全控件。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 不适用 | |
| VNet 注入支持| 不适用 | |
| 网络隔离和防火墙支持| 不适用 |  |
| 强制隧道支持| 不适用 | |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | [有关公共基本负载均衡器](load-balancer-monitor-log.md)，请参阅 Azure Monitor 日志。 |
| 控制和管理平面日志记录和审核| 是 | [有关公共基本负载均衡器](load-balancer-monitor-log.md)，请参阅 Azure Monitor 日志。 |
| 数据平面日志记录和审核 | 不适用 |  |

## <a name="identity"></a>标识

| 安全控制 | 是/否 | 说明|
|---|---|--|
| 身份验证| 不适用 |  |
| 授权| 不适用 |  |

## <a name="data-protection"></a>数据保护

| 安全控制 | 是/否 | 说明 |
|---|---|--|
| 服务器端加密： Microsoft 托管密钥 | 不适用 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 不适用 | |
| 服务器端静态加密：客户管理的密钥 (BYOK) | 不适用 | |
| 列级加密（Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | 是/否 | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 不适用 |  | 

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。