---
title: Azure Key Vault 的常见错误代码 |Microsoft Docs
description: Azure Key Vault 的常见错误代码
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876886"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Azure Key Vault 的常见错误代码

Azure 密钥保管库上的操作可能返回下表中列出的错误代码

| 错误代码 | 用户消息 |
|--|--|
| VaultAlreadyExists |  指定的密钥保管库已存在软删除状态 (或另一个订阅) 中。 |
| VaultNameNotValid |  保管库名称应为24个字符，字母数字，并以字母开头 |
| AccessDenied |  你可能在访问策略中缺少权限来执行该操作。 |
| ForbiddenByFirewall |  客户端地址未获得授权，并且调用方不是受信任的服务。 |
| ConflictError |  正在对同一项目请求多个操作。  |
| RegionNotSupported |  此资源不支持指定的 azure 区域。 |
| SkuNotSupported |  此资源不支持指定的 SKU 类型。 |
| ResourceNotFound |  找不到指定的 azure 资源。 |
| CertificateExpired |  检查证书的过期日期和有效期。 |


## <a name="next-steps"></a>后续步骤

- 参阅 [Azure Key Vault 开发人员指南](developers-guide.md)
- 了解有关[向密钥保管库进行身份验证](authentication.md)的详细信息
