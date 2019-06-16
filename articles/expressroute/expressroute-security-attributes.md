---
title: Azure ExpressRoute 的常见安全属性
description: 评估 Azure ExpressRoute 的常见安全属性进行的检查表
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083270"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Azure ExpressRoute 的常见安全属性

安全性已集成到 Azure 服务的各个方面。 本文介绍内置于 Azure ExpressRoute 的常见安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>|  不适用 | ExpressRoute 不存储客户数据。 |
| 传输中加密：<ul><li>快速路由加密</li><li>Vnet 中加密</li><li>VNet-VNet 加密</ul>| 否 | |
| 加密密钥处理（CMK、BYOK 等）| 不适用 |  |
| 列级加密（Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 不适用 |  |
| vNET 注入支持| 不适用 | |
| 网络隔离和防火墙支持| 是 | 每个客户是包含在其自己的路由域和隧道发送到其自身的 VNet |
| 强制隧道支持| 不适用 | 通过边界网关协议 (BGP)。 |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[ExpressRoute 监视指标和警报](expressroute-monitoring-metrics-alerts.md)。|

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | Microsoft (GWM) （控制器）; 网关的服务帐户只需在开发人员和 OP.的实时 (JIT) 访问权限 |
| 授权|  是 |Microsoft (GWM) （控制器）; 网关的服务帐户只需在开发人员和 OP.的实时 (JIT) 访问权限 |


## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明| 
|---|---|--|
| 控制和管理平面日志记录和审核| 是 |  |
| 数据平面日志记录和审核| 否 |   |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 通过网络资源提供程序 (NRP)。 |
