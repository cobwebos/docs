---
title: 常见的安全属性的 Azure 资源管理器
description: 评估 Azure 资源管理器的常见安全属性进行的检查表
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002258"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Azure 资源管理器的安全属性

本文介绍 Azure Resource Manager 中内置的安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| “是” |  |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| “是” | HTTPS/TLS。 |
| 加密密钥处理（CMK、BYOK 等）| 不适用 | Azure 资源管理器将没有客户内容，仅控制数据存储。 |
| 列级加密（Azure 数据服务）| “是” | |
| 加密的 API 调用| “是” | |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| “否” | |
| VNet 注入支持| “是” | |
| 网络隔离和防火墙支持| “否” |  |
| 强制隧道支持| “否” |  |

## <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| “否” | |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Authentication| “是” | [Azure Active Directory](/azure/active-directory)基于。|
| 授权| “是” | |


## <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| “是” | 公开所有其他写入操作 (PUT、 POST、 DELETE) 针对资源; 执行的活动日志请参阅[查看活动日志以审核对资源的操作](resource-group-audit.md)。 |
| 数据平面日志记录和审核| 不适用 | |

## <a name="configuration-management"></a>配置管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| “是” |  |
