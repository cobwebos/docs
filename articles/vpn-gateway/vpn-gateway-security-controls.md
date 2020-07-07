---
title: Azure VPN 网关的安全控制
description: 用于评估 Azure VPN 网关的安全控制的清单
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82127856"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Azure VPN 网关的安全控制

本文介绍 Azure VPN 网关中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 注释 |
|---|---|--|
| 服务终结点支持| 空值 | |
| VNet 注入支持| 空值 | |
| 网络隔离和防火墙支持| 是 | VPN 网关是每个客户虚拟网络的专用 VM 实例  |
| 强制隧道支持| 是 |  |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 注释|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[Azure Monitor 日志警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)  &  [Azure Monitor 指标警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。  |
| 控制和管理平面日志记录和审核| 是 | Azure 资源管理器活动日志。 |
| 数据平面日志记录和审核 | 是 | [Azure Monitor 活动日志](../azure-resource-manager/management/view-activity-logs.md)，用于 VPN 连接日志记录和审核。 |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 身份验证| 是 | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)，用于管理服务和配置 Azure VPN 网关。 |
| 授权| 是 | 支持通过 [RBAC](../role-based-access-control/overview.md) 进行授权。 |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 注释 |
|---|---|--|
| 服务器端静态加密：Microsoft 管理的密钥 | 空值 | VPN 网关传输客户数据，不存储客户数据 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | VPN 网关加密 Azure VPN 网关和客户本地 VPN 设备之间 (S2S) 或 VPN 客户端之间 (P2S) 的客户数据包。 VPN 网关还支持 VNet 到 VNet 加密。 |
| 服务器端静态加密：客户管理的密钥 (BYOK) | 否 | 客户指定的预共享密钥进行静态加密，但尚未与 CMK 集成。 |
| 列级加密（Azure 数据服务）| 空值 | |
| 加密的 API 调用| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS  |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 进行管理操作时，可以将 Azure VPN 网关配置的状态导出为 Azure 资源管理器模板，并在一段时间内进行版本控制。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[跨 Azure 服务的内置安全控制](../security/fundamentals/security-controls.md)。
