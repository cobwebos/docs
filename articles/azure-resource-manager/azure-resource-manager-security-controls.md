---
title: Azure 资源管理器的安全控制
description: 用于评估 Azure 资源管理器服务的内置安全控件清单。
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 78f3f41ca2f0aa45d295fd9846cf3aa96cccea72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390462"
---
# <a name="security-controls-for-azure-resource-manager"></a>Azure 资源管理器的安全控制

本文介绍 Azure 资源管理器中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务器端加密： Microsoft 托管密钥 | 是 |  |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | HTTPS/TLS。 |
| 服务器端加密：客户托管的密钥（BYOK） | N/A | Azure 资源管理器不存储任何客户内容，只能控制数据。 |
| 列级加密（Azure 数据服务）| 是 | |
| 加密的 API 调用| 是 | |

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| No | |
| VNet 注入支持| 是 | |
| 网络隔离和防火墙支持| No |  |
| 强制隧道支持| No |  |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log analytics、App insights 等）| No | |
| 控制和管理平面日志记录和审核| 是 | 活动日志公开对资源执行的所有写入操作（PUT、POST、DELETE）;请参阅[查看活动日志以审核对资源的操作](resource-group-audit.md)。 |
| 数据平面日志记录和审核| N/A | |

## <a name="identity"></a>身份标识

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | 基于[Azure Active Directory](/azure/active-directory) 。|
| 授权| 是 | |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 |  |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。