---
title: Azure Key Vault 的安全特性
description: 用于评估 Azure Key Vault 安全属性的清单
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 836d025c5bc69da9606c9a6172ac6a43caaaf29b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444017"
---
# <a name="security-attributes-for-azure-key-vault"></a>Azure Key Vault 的安全特性

本文记录了内置到 Azure Key Vault 中的安全特性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能）| 是 | 加密所有对象。 |
| 传输中的加密 (如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密)| 是 | 所有通信都通过加密的 API 调用进行 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户控制其密钥保管库中的所有密钥。 如果指定了硬件安全模块 (HSM) 支持的密钥，FIPS 2 级 HSM 会保护密钥、证书或机密。 |
| 列级加密（Azure 数据服务）| 不可用 |  |
| 加密的 API 调用| 是 | 使用 HTTPS。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 使用虚拟网络 (VNet) 服务终结点。 |
| VNet 注入支持| 否 |  |
| 网络隔离和防火墙支持| 是 | 使用 VNet 防火墙规则。 |
| 强制隧道支持| 否 |  |

## <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 使用 Log Analytics。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 身份验证| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| Authorization| 是 | 使用密钥保管库访问策略。 |


## <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制/管理平面日志记录和审核| 是 | 使用 Log Analytics。 |
| 数据平面日志记录和审核| 是 | 使用 Log Analytics。 |

## <a name="access-controls"></a>访问控制

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制/管理平面访问控制 | 是 | Azure Resource Manager 基于角色的访问控制 (RBAC) |
| 数据平面访问控制（在每个服务级别） | 是 | 密钥保管库访问策略 |