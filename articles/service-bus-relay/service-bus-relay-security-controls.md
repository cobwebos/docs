---
title: Azure 服务总线中继的安全控制
description: 本文提供了用于评估 Azure 服务总线中继的内置安全控件清单。
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514011"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Azure 服务总线中继的安全控制

本文介绍了 Azure 服务总线中继中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 | 文档 |
|---|---|--|--|
| 服务终结点支持| 否 |  |   |
| 网络隔离和防火墙支持| 否 |  |   |
| 强制隧道支持| N/A | 中继是 TLS 隧道  |   |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| Azure 监视支持（Log analytics、App insights 等）| 是 | |   |
| 控制和管理平面日志记录和审核| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)。 |   |
| 数据平面日志记录和审核| 是 | 连接成功/失败和错误，并已记录。  |   |

## <a name="identity"></a>身份标识

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| 身份验证| 是 | 通过 SAS。 | [Azure 中继身份验证和授权](relay-authentication-and-authorization.md) |
| 授权|  是 | 通过 SAS。 | [Azure 中继身份验证和授权](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 | 文档 |
|---|---|--|--|
| 服务器端加密： Microsoft 托管密钥 |  N/A | 中继是一个 web 套接字，不会保留数据。 |   |
| 服务器端加密：客户托管的密钥（BYOK） | 否 | 仅使用 Microsoft TLS 证书。  |   |
| 列级加密（Azure 数据服务）| N/A | |   |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 服务需要 TLS。 |   |
| 加密的 API 调用| 是 | HTTPS. |


## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| 配置管理支持（配置的版本控制等）| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)。|   |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。