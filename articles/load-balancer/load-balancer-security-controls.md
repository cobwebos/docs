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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214900"
---
# <a name="security-controls-for-azure-load-balancer"></a>Azure 负载均衡器的安全控制

本文介绍 Azure 负载均衡器中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 空值 | |
| VNet 注入支持| 空值 | |
| 网络隔离和防火墙支持| 空值 |  |
| 强制隧道支持| 空值 | |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 有关[公共基本负载平衡器，请参阅 Azure 监视器日志](load-balancer-monitor-log.md)。 |
| 控制和管理平面日志记录和审核| 是 | 有关[公共基本负载平衡器，请参阅 Azure 监视器日志](load-balancer-monitor-log.md)。 |
| 数据平面日志记录和审核 | 空值 |  |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 身份验证| 空值 |  |
| 授权| 空值 |  |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务器端静态加密：微软管理的密钥 | 空值 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 空值 | |
| 服务器端静态加密：客户管理的密钥 (BYOK) | 空值 | |
| 列级加密（Azure 数据服务）| 空值 | |
| 加密的 API 调用| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 空值 |  | 

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure[服务中的内置安全控件](../security/fundamentals/security-controls.md)。