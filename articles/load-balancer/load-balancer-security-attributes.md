---
title: Azure 负载均衡器的安全属性
description: 用于评估负载均衡器的安全属性清单
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440873"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Azure 负载均衡器的安全属性

本文介绍了 Azure 负载均衡器中内置的安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 不可用 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 不可用 | |
| 加密密钥处理（CMK、BYOK 等）| 不可用 | |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 不可用 | |
| VNet 注入支持| 不可用 | . |
| 网络隔离和防火墙支持| 不可用 |  |
| 强制隧道支持| 不可用 | |

## <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | [有关公共基本负载均衡器](load-balancer-monitor-log.md), 请参阅 Azure Monitor 日志。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 身份验证| 不可用 |  |
| Authorization| 不可用 |  |


## <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | [有关公共基本负载均衡器](load-balancer-monitor-log.md), 请参阅 Azure Monitor 日志。 |
| 数据平面日志记录和审核 | 不可用 |  |

## <a name="configuration-management"></a>配置管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 不可用 |  | 
