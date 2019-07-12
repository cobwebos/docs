---
title: Azure 负载均衡器的安全属性
description: 安全评估负载均衡器的属性进行的检查表
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800076"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Azure 负载均衡器的安全属性

本文介绍 Azure 负载均衡器中内置的常见安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 不可用 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 不可用 | |
| 加密密钥处理（CMK、BYOK 等）| 不可用 | |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 不可用 | |
| VNet 注入支持| 不可用 | . |
| 网络隔离和防火墙支持| 不可用 |  |
| 强制隧道支持| 不可用 | |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[的公共基本负载均衡器的 Azure Monitor 日志](load-balancer-monitor-log.md)。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 不可用 |  |
| Authorization| 不可用 |  |


## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 请参阅[的公共基本负载均衡器的 Azure Monitor 日志](load-balancer-monitor-log.md)。 |
| 数据平面日志记录和审核 | 不可用 |  |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 不可用 |  | 
