---
title: Azure 中继的安全控制
description: 本文提供了用于评估 Azure 中继的内置安全控件清单。
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: f8165d994e998af4f15cd6aa2fd08b75191b8b64
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211456"
---
# <a name="security-controls-for-azure-relay"></a>Azure 中继的安全控制

本文介绍了 Azure 中继中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 注释 | 文档 |
|---|---|--|--|
| 服务终结点支持| 否 |  |   |
| 网络隔离和防火墙支持| 否 |  |   |
| 强制隧道支持| 不适用 | 中继是 TLS 隧道  |   |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 注释| 文档 |
|---|---|--|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | |   |
| 控制和管理平面日志记录和审核| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)。 |   |
| 数据平面日志记录和审核| 是 | 连接成功/失败、错误和记录的内容。  |   |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 注释| 文档 |
|---|---|--|--|
| 身份验证| 是 | 通过 SAS。 | [Azure 中继身份验证和授权](relay-authentication-and-authorization.md) |
| 授权|  是 | 通过 SAS。 | [Azure 中继身份验证和授权](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 注释 | 文档 |
|---|---|--|--|
| 服务器端静态加密：Microsoft 管理的密钥 |  不适用 | 中继是一个 Web 套接字，不保存数据。 |   |
| 服务器端静态加密：客户管理的密钥 (BYOK) | 否 | 仅使用 Microsoft TLS 证书。  |   |
| 列级加密（Azure 数据服务）| 空值 | |   |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 服务需要 TLS。 |   |
| 加密的 API 调用| 是 | HTTPS。 |


## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 注释| 文档 |
|---|---|--|--|
| 配置管理支持（配置的版本控制等）| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)。|   |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。