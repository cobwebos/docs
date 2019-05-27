---
title: Azure Key Vault 的常见安全特性
description: 用于计算 Azure 密钥保管库的常见安全特性的清单
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c2265ff5f4c444121bf70c35145703f1b9fe981
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000179"
---
# <a name="security-attributes-for-azure-key-vault"></a>Azure Key Vault 的安全特性

本文介绍 Azure 密钥保管库中内置的安全属性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| “是” | 加密所有对象。 |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| “是” | 所有通信都通过加密的 API 调用进行 |
| 加密密钥处理（CMK、BYOK 等）| “是” | 客户控制其密钥保管库中的所有密钥。 如果指定了硬件安全模块 (HSM) 支持的密钥，FIPS 2 级 HSM 会保护密钥、证书或机密。 |
| 列级加密（Azure 数据服务）| 不适用 |  |
| 加密的 API 调用| “是” | 使用 HTTPS。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| “是” | 使用虚拟网络 (VNet) 服务终结点。 |
| VNet 注入支持| “否” |  |
| 网络隔离和防火墙支持| “是” | 使用 VNet 防火墙规则。 |
| 强制隧道支持| “否” |  |

## <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| “是” | 使用 Log Analytics。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Authentication| “是” | 身份验证通过 Azure Active Directory 来进行。 |
| 授权| “是” | 使用密钥保管库访问策略。 |


## <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制/管理平面日志记录和审核| “是” | 使用 Log Analytics。 |
| 数据平面日志记录和审核| “是” | 使用 Log Analytics。 |

## <a name="access-controls"></a>访问控制

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制/管理平面访问控制 | “是” | Azure Resource Manager 基于角色的访问控制 (RBAC) |
| 数据平面访问控制（在每个服务级别） | “是” | 密钥保管库访问策略 |