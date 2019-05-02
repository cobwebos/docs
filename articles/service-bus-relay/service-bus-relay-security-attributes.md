---
title: Azure 服务总线中继的常见安全属性
description: 评估 Azure 服务总线中继的常见安全属性进行的检查表
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: f8827ac290393c9f394c3b13149555a1a2aa6df9
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927487"
---
# <a name="common-security-attributes-for-azure-service-bus-relay"></a>Azure 服务总线中继的常见安全属性

安全性已集成到 Azure 服务的各个方面。 本文介绍 Azure 服务总线中继中内置的常见安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>|  不适用 | 中继是一个 web 套接字，不会保留数据。 |
| 传输中加密：<ul><li>快速路由加密</li><li>Vnet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 服务需要 TLS。 |
| 加密密钥处理 （CMK，BYOK，等等。）| 否 | 使用 Microsoft TLS 证书。  |
| 列级加密 （Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | HTTPS。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 |  |
| 网络隔离和防火墙支持| 否 |  |
| 强制隧道支持| 不适用 | 中继是 TLS 隧道  |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | 通过 SAS。 |
| 授权|  是 | 通过 SAS。 |


## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)。 |
| 数据平面日志记录和审核| 是 | 连接成功 / 失败和错误和记录。  |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持 （版本控制的配置，等等）。| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)。|
