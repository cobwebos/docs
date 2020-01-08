---
title: Azure Service Fabric 的安全控制
description: 了解 Azure Service Fabric 的安全控制。 包含内置安全控件清单。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645423"
---
# <a name="security-controls-for-azure-service-fabric"></a>Azure Service Fabric 的安全控制

本文介绍 Azure Service Fabric 中内置的安全控制。 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 是 |  |
| 网络隔离和防火墙支持| 是 | 使用网络安全组 (NSG)。 |
| 强制隧道支持| 是 | Azure 网络支持强制隧道。 |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log analytics、App insights 等）| 是 | 使用 Azure 监视支持和第三方支持。 |
| 控制和管理平面日志记录和审核| 是 | 所有控制平面操作都需经过审核和审批流程。 |
| 数据平面日志记录和审核| N/A | 客户拥有群集。  |

## <a name="identity"></a>身份标识

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| 授权| 是 | 通过 SFRP 进行针对调用的标识和访问管理 (IAM)。 直接调用群集终结点支持两个角色： User 和 Admin。客户可将 Api 映射到任一角色。 |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务器端加密： Microsoft 托管密钥 | 是 | 客户拥有群集和虚拟机规模集（在其上构建群集）。 可在虚拟机规模集上启用 Azure 磁盘加密。 |
| 服务器端加密：客户托管的密钥（BYOK） | 是 | 客户拥有群集和虚拟机规模集（在其上构建群集）。 可在虚拟机规模集上启用 Azure 磁盘加密。 |
| 列级加密（Azure 数据服务）| N/A |  |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 |  |
| 加密的 API 调用| 是 | Service Fabric API 调用通过 Azure 资源管理器进行。 需要有效 JSON web 令牌 (JWT)。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。