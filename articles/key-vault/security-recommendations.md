---
title: 适用于 Azure Key Vault 的安全建议
description: 适用于 Azure Key Vault 的安全建议。 实施此指南将有助于你履行我们的共享职责模型中描述的安全职责
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 9fd41ae9b61a9c74fd5d99dd71199cfde06f5cb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192364"
---
# <a name="security-recommendations-for-azure-key-vault"></a>适用于 Azure Key Vault 的安全建议

本文包含适用于 Azure Key Vault 的安全建议。 实施执行建议将有助于你履行我们的共享职责模型中描述的安全职责。 若要详细了解 Microsoft 采取哪些措施来履行服务提供商责任，请阅读[云计算的责任分担](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

包含在本文中的某些建议可能受 Azure 安全中心的自动监视。 在保护你在 Azure 中的资源方面，Azure 安全中心是第一道防线。 它定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向你提供有关如何解决这些安全漏洞的建议。

- 有关 Azure 安全中心建议的详细信息，请参阅 [Azure 安全中心的安全性建议](../security-center/security-center-recommendations.md)。
- 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../security-center/security-center-intro.md)

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 安全中心 |
|-|----|--|
|启用软删除 | [软删除](key-vault-ovw-soft-delete.md)允许恢复已删除的保管库和保管库对象 |  - |
| 限制对保管库数据的访问  | 遵循“最低访问权限”原则，限制组织成员对保管库数据的访问 |  - |

## <a name="identity-and-access-management"></a>标识和访问管理

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 限制具有“参与者”访问权限的用户数 | 如果用户具有密钥保管库管理平面的“参与者权”限，则该用户可以通过设置 Key Vault 访问策略来授予自己对数据平面的访问权限。 应严格控制对密钥保管库具有“参与者”角色访问权限的用户。 请确保只有那些需要访问已授权人员的用户才能访问和管理保管库。 可以阅读[保护对密钥保管库的访问](key-vault-secure-your-key-vault.md) | - |

## <a name="monitoring"></a>监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
 应启用 Key Vault 中的诊断日志 | 启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 | [是](../security-center/security-center-identity-access.md) |
| 限制可以访问 Azure Key Vault 日志的用户 | [Key Vault 日志](key-vault-logging.md)保存针对保管库执行的活动（例如创建或删除保管库、密钥、机密）的相关信息，这些信息可以在调查过程中使用。 |  - |

## <a name="networking"></a>网络

| 建议 | 注释 | 安全中心 |
|-|----|--|
|限制网络暴露 | 网络访问应该仅限需要进行保管库访问的解决方案所使用的虚拟网络。 查看有关 [Azure Key Vault 虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)的信息 | - |

## <a name="next-steps"></a>后续步骤

请咨询应用程序提供商，看是否有其他安全要求。 有关开发安全应用程序的详细信息，请参阅[安全开发文档](../security/fundamentals/abstract-develop-secure-apps.md)。
