---
title: Azure 事件中心的安全属性
description: 用于评估 Azure 事件中心的安全属性清单
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbaldwin
ms.openlocfilehash: 2262609de774eb2b1334215bf46968b5554ed691
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442452"
---
# <a name="security-attributes-for-azure-event-hubs"></a>Azure 事件中心的安全属性

本文介绍了 Azure 事件中心中内置的安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能）|  是 | |
| 传输中的加密 (如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密)| 是 | |
| 加密密钥处理（CMK、BYOK 等）| 否 |  |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 |  |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 否 | |
| 网络隔离和防火墙支持| 是 |  |
| 强制隧道支持| 否 |  |

## <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 身份验证| 是 | |
| Authorization|  是 | |


## <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 |  |
| 数据平面日志记录和审核| 是 |   |

## <a name="configuration-management"></a>配置管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | |
