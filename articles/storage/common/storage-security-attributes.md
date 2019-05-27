---
title: Azure 存储的常见安全属性
description: 用于评估 Azure 存储的常见安全属性的清单
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 922273e3805004f6af068ea748c16f5675810144
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001463"
---
# <a name="security-attributes-for-azure-storage"></a>Azure 存储的安全属性

本文介绍 Azure 存储内置的安全属性。 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| “是” |  |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| “是” | 支持标准的 HTTPS/TLS 机制。  用户也可以先加密数据，然后再将其传输到服务。 |
| 加密密钥处理（CMK、BYOK 等）| “是” | 请参阅[在 Azure Key Vault 中使用客户托管密钥进行存储服务加密](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 列级加密（Azure 数据服务）| 不适用 |  |
| 加密的 API 调用| “是” |  |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| “是” |  |
| VNet 注入支持| 不适用 |  |
| 网络隔离和防火墙支持| “是” | |
| 强制隧道支持| 不适用 |  |

## <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| “是” | Azure Monitor 指标现已发布，日志开始推出预览版 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Authentication| “是” | Azure Active Directory、共享密钥、共享访问令牌。 |
| 授权| “是” | 支持通过 RBAC、POSIX ACL 和 SAS 令牌进行授权 |


## <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核 | “是” | Azure 资源管理器活动日志 |
| 数据平面日志记录和审核| “是” | 服务诊断日志，Azure Monitor 日志记录开始推出预览版  |

## <a name="configuration-management"></a>配置管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| “是” | 支持通过 Azure 资源管理器 API 进行资源提供程序版本控制 |