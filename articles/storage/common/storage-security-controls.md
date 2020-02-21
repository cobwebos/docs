---
title: Azure 存储的安全控制
description: 用于评估 Azure 存储的安全控制清单
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2cc54077456fce1e7e0f47843a762beee8e715f7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526750"
---
# <a name="security-controls-for-azure-storage"></a>Azure 存储的安全控制

本文介绍内置于 Azure 存储中的安全控件。 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务器端加密： Microsoft 托管密钥 | 是 |  |
| 服务器端加密：客户托管的密钥（BYOK） | 是 | 请参阅[Azure Key Vault 中使用客户托管密钥的存储服务加密](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 列级加密（Azure 数据服务）| 空值 |  |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 支持标准 HTTPS/TLS 机制。  用户还可以在将数据传输到服务之前对其进行加密。 |
| 加密的 API 调用| 是 |  |

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 空值 |  |
| 网络隔离和防火墙支持| 是 | |
| 强制隧道支持| 空值 |  |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log analytics、App insights 等）| 是 | Azure Monitor 度量值|
| 控制和管理平面日志记录和审核 | 是 | Azure 资源管理器活动日志 |
| 数据平面日志记录和审核| 是 | 服务诊断日志。|

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | Azure Active Directory、共享密钥、共享访问令牌。 |
| 授权| 是 | 支持通过 RBAC、POSIX Acl 和 SAS 令牌进行授权 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过 Azure 资源管理器 Api 进行资源提供程序版本控制 |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../../security/fundamentals/security-controls.md)。