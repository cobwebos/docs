---
title: Azure 虚拟机规模集的安全控制
description: 用于评估 Azure 虚拟机规模集的安全控制清单
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029410"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Azure 虚拟机规模集的安全控制

本文介绍 Azure 虚拟机规模集中内置的安全控制。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | |
| VNet 注入支持| 是 | |
| 网络隔离和防火墙支持| 是 |  |
| 强制隧道支持| 是 | 请参阅[使用 Azure 资源管理器部署模型配置强制隧道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)。 |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[在 Azure 中监视和更新 Linux 虚拟机](../virtual-machines/linux/tutorial-monitor.md)和[在 Azure 中监视和更新 Windows 虚拟机](../virtual-machines/windows/tutorial-monitor.md)。 |
| 控制和管理平面日志记录和审核| 是 |  |
| 数据平面日志记录和审核 | 否 |  |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 |  |
| 授权| 是 |  |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务器端静态加密：Microsoft 托管密钥 | 是 | 请参阅[适用于虚拟机规模集的 Azure 磁盘加密](disk-encryption-overview.md)。 |
| 传输中加密（如 ExpressRoute 加密、VNet 内加密和 VNet-VNet 加密）| 是 | Azure 虚拟机支持 [ExpressRoute](../expressroute/index.yml) 和 VNet 加密。 请参阅 [VM 中的传输中加密](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)。 |
| 服务器端静态加密：客户管理的密钥 (BYOK) | 是 | 客户管理的密钥是受支持的 Azure 加密方案；请参阅[适用于虚拟机规模集的 Azure 磁盘加密](disk-encryption-overview.md)|
| 列级加密（Azure 数据服务）| 空值 | |
| 加密的 API 调用| 是 | 通过 HTTPS 和 TLS。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 |  | 

## <a name="next-steps"></a>后续步骤

- 详细了解[跨 Azure 服务的内置安全控制](../security/fundamentals/security-controls.md)。
