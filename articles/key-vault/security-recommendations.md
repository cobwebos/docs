---
title: Azure Key Vault 的安全性建议
description: Azure Key Vault 的安全建议。 实施本指南可帮助你满足我们的共享责任模型中所述的安全要求
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 09ccfd6e344f2776cfedfc56976f2a5c34f79d5c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428178"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure Key Vault 的安全性建议

本文包含有关 Azure Key Vault 的安全建议。 实施这些建议可帮助你满足我们的共享责任模型中所述的安全要求。 若要详细了解 Microsoft 如何满足服务提供商的责任，请阅读[云计算的共享责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf)。

Azure 安全中心会自动监视本文中包含的一些建议。 Azure 安全中心是保护 Azure 中资源的第一道防线。 它会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向您提供有关如何解决这些问题的建议。

- 有关 Azure 安全中心建议的详细信息，请参阅[Azure 安全中心中的安全建议](../security-center/security-center-recommendations.md)。
- 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../security-center/security-center-intro.md)

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 安全中心 |
|-|----|--|
|启用软删除 | [软删除](key-vault-ovw-soft-delete.md)使你可以恢复已删除的保管库和保管库对象 |  - |
| 限制对保管库数据的访问  | 遵循最低权限原则，并限制组织的哪些成员有权访问保管库数据 |  - |

## <a name="identity-and-access-management"></a>身份和访问管理

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 限制具有参与者访问权限的用户数 | 如果用户具有对密钥保管库管理平面的参与者权限，则该用户可以通过设置 Key Vault 访问策略来授予自己对数据平面的访问权限。 你应严格控制谁有权访问你的密钥保管库的角色。 请确保只有那些需要访问授权人员才能访问和管理保管库的人员。 你可以读取[对密钥保管库的安全访问](key-vault-secure-your-key-vault.md) | - |

## <a name="monitoring"></a>监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
 应启用 Key Vault 中的诊断日志 | 启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 | [是](../security-center/security-center-identity-access.md) |
| 限制可以访问 Azure 密钥保管库日志的人员 | [Key Vault 日志](key-vault-logging.md)保存有关在保管库中执行的活动的信息，例如创建或删除保管库、密钥、机密，并可在调查过程中使用 |  - |

## <a name="networking"></a>网络

| 建议 | 注释 | 安全中心 |
|-|----|--|
|限制网络曝光 | 网络访问应限制为需要保管库访问的解决方案所使用的虚拟网络。 查看有关[Azure Key Vault 的虚拟网络服务终结点的](key-vault-overview-vnet-service-endpoints.md)信息 | - |

## <a name="next-steps"></a>后续步骤

请与应用程序提供商联系，查看是否存在其他安全要求。 有关开发安全应用程序的详细信息，请参阅[安全开发文档](../security/fundamentals/abstract-develop-secure-apps.md)。
