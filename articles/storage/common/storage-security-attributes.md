---
title: Azure 存储的常见安全属性
description: 常见的安全属性，用于评估 Azure 存储进行的检查表
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7868b52fee991d4b9323fa0b7969aeca4dc83cdb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711954"
---
# <a name="common-security-attributes-for-azure-storage"></a>Azure 存储的常见安全属性

安全性已集成到 Azure 服务的各个方面。 本文介绍 Azure 存储内置的常见安全属性。 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 |  |
| 传输中加密：<ul><li>快速路由加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 支持标准的 HTTPS/TLS 机制。  传输到服务之前，用户还可以加密数据。 |
| 加密密钥处理 （CMK，BYOK，等等。）| 是 | 请参阅[使用 Azure Key Vault 中客户托管密钥的存储服务加密](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 列级加密 （Azure 数据服务）| 不适用 |  |
| 加密的 API 调用| 是 |  |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 不适用 |  |
| 网络隔离和防火墙支持| 是 | |
| 强制隧道支持| 不适用 |  |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | 可用的 azure Monitor 指标现在，可以记录开始预览 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | Azure Active Directory、 共享密钥、 共享访问令牌。 |
| 授权| 是 | 支持通过 RBAC、 POSIX Acl 和 SAS 令牌的授权 |


## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核 | 是 | Azure 资源管理器活动日志 |
| 数据平面日志记录和审核| 是 | 服务诊断日志和 Azure Monitor 日志记录开始预览  |

## <a name="configuration-management"></a>配置管理

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持 （版本控制的配置，等等）。| 是 | 支持通过 Azure 资源管理器 Api 的资源提供程序版本控制 |