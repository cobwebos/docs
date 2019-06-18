---
title: Azure VPN 网关的安全属性
description: 评估 Azure VPN 网关的安全属性进行的检查表
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083127"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Azure VPN 网关的安全属性

本文介绍内置于 Azure VPN 网关的公共安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 不适用 | VPN 网关传输客户数据，不会存储客户数据 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | VPN 网关对客户 Azure VPN 网关和客户的本地 VPN 设备 (S2S) 或 VPN 客户端 (P2S) 之间的数据包进行加密。 VPN 网关也支持 VNet 到 VNet 加密。 |
| 加密密钥处理（CMK、BYOK 等）| 否 | 客户指定预共享的密钥进行加密的静态;但未尚未集成与 CMK。 |
| 列级加密（Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS  |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 不适用 | |
| VNet 注入支持| 不适用 | . |
| 网络隔离和防火墙支持| 是 | VPN 网关是为每个客户虚拟网络的专用的 VM 实例  |
| 强制隧道支持| 是 |  |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[Azure Monitor 诊断日志/警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor 指标/警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。  |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)管理服务和配置 Azure VPN 网关。 |
| 授权| 是 | 支持通过授权[RBAC](../role-based-access-control/overview.md)。 |


## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | Azure 资源管理器活动日志。 |
| 数据平面日志记录和审核 | 是 | [Azure Monitor 诊断日志](../azure-resource-manager/resource-group-audit.md)针对 VPN 连接日志记录和审核。 |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 对于管理操作，Azure VPN 网关配置的状态可以导出为 Azure 资源管理器模板和版本控制随着时间的推移。 | 