---
title: Azure Key Vault 安全性 |Microsoft Docs
description: 管理 Azure Key Vault、密钥和机密的访问权限。 介绍 Key Vault 的身份验证和授权模型以及如何保护 Key Vault。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 3cc5cb68f430ac8e5070b9c8c4a1aa0c28639311
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270896"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault 安全性

需要保护云中的加密密钥和机密（例如证书、连接字符串和密码），以便使用 Azure Key Vault。 由于你要存储敏感和业务关键数据，因此你需要采取措施来最大程度地提高保管库和存储的数据的安全性。 本文将介绍在设计 Azure Key Vault 安全性时应考虑的一些概念。

## <a name="identity-and-access-management"></a>标识和访问管理

在 Azure 订阅中创建密钥保管库时，该密钥保管库自动与订阅的 Azure AD 租户关联。 尝试管理或检索保管库内容的任何人都必须通过 Azure AD 进行身份验证。

- 身份验证可确定调用方的身份。
- 授权确定调用方可以执行哪些操作。 Key Vault 中的授权结合使用[基于角色的访问控制](../role-based-access-control/overview.md)（RBAC）和 Azure Key Vault 访问策略。

### <a name="access-model-overview"></a>访问模型概述

可以通过两个接口或平面访问保管库。 这些平面是管理平面和数据平面。

- *管理平面*是您管理 Key Vault 自身的位置，它是用于创建和删除保管库的接口。 还可以读取 key vault 属性并管理访问策略。
- *数据平面*允许使用存储在密钥保管库中的数据。 可以添加、删除和修改密钥、机密及证书。

若要在任一平面中访问密钥保管库，必须对所有调用方（用户或应用程序）进行身份验证和授权。 对于身份验证，这两个平面都使用 Azure Active Directory (Azure AD)。 对于授权，管理平面使用基于角色的访问控制 (RBAC)，而数据平面使用密钥保管库访问策略。

对这两种平面使用单一身份验证机制模型具有多个优点：

- 组织可以集中控制对其组织中的所有密钥保管库的访问。
- 离职的用户会立即失去对组织中所有密钥保管库的访问权限。
- 组织可以使用 Azure AD 中的选项自定义身份验证，例如启用多重身份验证以提高安全性

### <a name="managing-administrative-access-to-key-vault"></a>管理对 Key Vault 的管理访问权限

在资源组中创建密钥保管库时，可以使用 Azure AD 来管理访问权限。 授予用户或组管理资源组中的密钥保管库的权限。 可以通过分配相应的 RBAC 角色，在特定范围级别授予访问权限。 若要授予用户管理密钥保管库的访问权限，请为特定范围的用户分配预定义的 `key vault Contributor` 角色。 可以将以下范围级别分配给 RBAC 角色：

- **订阅**：在订阅级别分配的 RBAC 角色适用于该订阅中的所有资源组和资源。
- **资源组**：在资源组级别分配的 RBAC 角色适用于该资源组中的所有资源。
- **特定资源**：为特定资源分配的 RBAC 角色适用于该资源。 在这种情况下，资源是特定的密钥保管库。

有多种预定义角色。 如果预定义角色不符合需求，可以定义自己的角色。 有关详细信息，请参阅[RBAC：内置角色](../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果用户具有密钥保管库管理平面的 `Contributor` 权限，则该用户可以通过设置密钥保管库访问策略来授予自己对数据平面的访问权限。 应严格控制对密钥保管库具有 `Contributor` 角色访问权限的用户。 请确保仅授权的人员才能访问和管理 Key Vault、密钥、机密和证书。

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>控制对 Key Vault 数据的访问

Key Vault 访问策略分别向密钥、机密或证书授予权限。 可以仅授予用户对密钥的访问权限，而不授予对机密的访问权限。 密钥、机密和证书的访问权限在保管库级别进行管理。

> [!IMPORTANT]
> 密钥保管库访问策略不支持粒度、对象级别权限，例如特定的密钥、机密或证书。 如果授予某个用户创建和删除密钥的权限，该用户可以针对该密钥保管库中的所有密钥执行这些操作。

若要为密钥保管库设置访问策略，可以使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI 工具](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs) 或[密钥保管库管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)。

可以通过使用[适用于 Azure 密钥保管库的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)来限制数据平面访问权限。 可以配置[防火墙和虚拟网络规则](key-vault-network-security.md)以提供额外的安全层。

## <a name="network-access"></a>网络访问

可以通过指定哪些 IP 地址有权访问来减少保管库的公开。 通过 Azure Key Vault 的虚拟网络服务终结点可将访问限制为指定虚拟网络。 此外，还可通过这些终结点将访问限制为一系列 IPv4（Internet 协议版本 4）地址范围。 任何从外部连接到 Key Vault 的用户都无法访问这些资源。

防火墙规则生效后，如果用户的请求来自允许的虚拟网络或 IPv4 地址范围，则用户只能从 Key Vault 读取数据。 从 Azure 门户访问 Key Vault 时，这同样适用。 虽然用户可从 Azure 门户浏览到 Key Vault，但如果其客户端计算机不在允许列表中，则可能无法列出密钥、机密或证书。 这也会影响其他 Azure 服务的 Key Vault 选取器。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看 Key Vault 列表，但不能查看列表密钥。

有关 Azure Key Vault 网络地址查看[Azure Key Vault 的虚拟网络服务终结点的](key-vault-overview-vnet-service-endpoints.md)详细信息

## <a name="monitoring"></a>监视

Key Vault 日志记录会保存有关在保管库中执行的活动的信息。 Key Vault 日志：

- 所有经过身份验证的 REST API 请求，包括失败的请求
  - 对密钥保管库本身的操作。 这些操作包括创建、删除、设置访问策略和更新密钥保管库属性（如标记）。
  - 对 Key Vault 中的密钥和机密执行的操作，包括：
    - 创建、修改或删除这些密钥或机密。
    - 签名、验证、加密、解密、包装和解包密钥、获取机密、列出密钥和机密（及其版本）。
- 导致出现 401 响应的未经身份验证的请求。 例如，请求不包含持有者令牌、格式不正确或已过期，或者包含无效的令牌。

可以在密钥保管库操作完成后10分钟内访问日志记录信息。 你需要负责管理存储帐户中的日志。

- 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
- 删除不想继续保留在存储帐户中的日志。

有关安全管理存储帐户的建议，请查看[Azure 存储安全指南](../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)
- [RBAC：内置角色](../role-based-access-control/built-in-roles.md)
- [Azure Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)
