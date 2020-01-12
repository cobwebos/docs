---
title: Azure 事件中心的安全控制
description: 用于评估 Azure 事件中心的安全控制清单
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: ae357d25a37e188ed043aaa0ca750bb0e52d58da
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903548"
---
# <a name="security-controls-for-azure-event-hubs"></a>Azure 事件中心的安全控制

本文介绍了 Azure 事件中心中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 | 文档 |
|---|---|--|--|
| 服务终结点支持| 是 |  |  |
| VNet 注入支持| 否 | |  |
| 网络隔离和防火墙支持| 是 |  |  |
| 强制隧道支持| 否 |  |  |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| Azure 监视支持（Log analytics、App insights 等）| 是 | |  |
| 控制和管理平面日志记录和审核| 是 |  |  |
| 数据平面日志记录和审核| 是 |   |  |

## <a name="identity"></a>身份标识

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| 身份验证| 是 | | [授予对 Azure 事件中心的访问权限](authorize-access-event-hubs.md)，[使用 Azure Active Directory 授予对事件中心资源的访问权限](authorize-access-azure-active-directory.md)，[使用共享访问签名授予对事件中心资源的访问权限](authorize-access-shared-access-signature.md) |
| 授权|  是 | | [使用 Azure Active Directory 对托管标识所具有的事件中心资源访问权限进行验证](authenticate-managed-identity.md)，[使用 Azure Active Directory 对应用程序所具有的事件中心资源访问权限进行验证](authenticate-application.md)，[使用共享访问签名 (SAS) 验证对事件中心资源的访问权限](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 | 文档 |
|---|---|--|--|
| 服务器端加密： Microsoft 托管密钥 |  是 | |  |
| 服务器端加密：客户托管的密钥（BYOK） | 可以。 适用于专用群集。 | 可以使用 Azure KeyVault 中的客户托管密钥来加密静态事件中心的数据。 | [配置客户托管的密钥，以便通过使用 Azure 门户来加密静态 Azure 事件中心数据](configure-customer-managed-key.md) |
| 列级加密（Azure 数据服务）| N/A | |  |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | |  |
| 加密的 API 调用| 是 |  |  |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| 配置管理支持（配置的版本控制等）| 是 | |  |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。
