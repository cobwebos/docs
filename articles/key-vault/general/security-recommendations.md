---
title: Azure Key Vault 安全建议
description: Azure Key Vault 安全建议。 实施本文中的指导可帮助你履行我们的责任分担模型中所述的安全义务
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 0c541c848290988bb1f358f045abb2d0cd221de7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87386752"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Azure Key Vault 安全建议

本文包含针对 Azure Key Vault 的安全建议。 实施执行建议将有助于你履行我们的共享职责模型中描述的安全职责。 若要详细了解 Microsoft 采取哪些措施来履行服务提供商责任，请阅读[云计算的责任分担](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

包含在本文中的某些建议可能受 Azure 安全中心的自动监视。 在保护你在 Azure 中的资源方面，Azure 安全中心是第一道防线。 它定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向你提供有关如何解决这些安全漏洞的建议。

- 有关 Azure 安全中心建议的详细信息，请参阅 [Azure 安全中心的安全性建议](../../security-center/security-center-recommendations.md)。
- 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../../security-center/security-center-intro.md)

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 安全中心 |
|-|----|--|
|启用软删除 | 启用[软删除](soft-delete-overview.md)后，你可以恢复已删除的保管库和保管库对象 |  - |
| 限制对保管库数据的访问  | 遵循最低权限原则，且仅授权组织的部分成员访问保管库数据 |  - |

## <a name="identity-and-access-management"></a>标识和访问管理

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 限制拥有参与者访问权限的用户数 | 如果某个用户对 Key Vault 管理平面拥有参与者权限，则该用户可以通过设置 Key Vault 访问策略来授予自己对数据平面的访问权限。 应严格控制对 Key Vault 拥有“参与者”角色访问权限的用户。 确保只有已获授权且有必要进行访问的人员可以访问和管理你的保管库。 可以阅读[保护对 Key Vault 的访问](secure-your-key-vault.md) | - |

## <a name="monitoring"></a>监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
 应启用 Key Vault 中的诊断日志 | 启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 | [是](../../security-center/security-center-identity-access.md) |
| 限制谁可以访问 Azure Key Vault 日志 | [Key Vault 日志](logging.md)保存有关对保管库执行的活动（例如创建或删除保管库、密钥、机密）的信息，在调查期间可以使用这些信息 |  - |

## <a name="networking"></a>网络

| 建议 | 注释 | 安全中心 |
|-|----|--|
|限制网络曝光 | 应该仅允许通过需要访问保管库的解决方案所用的虚拟网络进行网络访问。 查看有关 [Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)的信息 | - |

## <a name="next-steps"></a>后续步骤

咨询你的应用程序提供商，以确定是否还要满足其他安全要求。 有关开发安全的应用程序的详细信息，请参阅[安全开发文档](../../security/fundamentals/abstract-develop-secure-apps.md)。
