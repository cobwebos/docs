---
title: Azure 密钥保管库安全 |Microsoft Docs
description: 管理 Azure Key Vault、密钥和机密的访问权限。 介绍 Key Vault 的身份验证和授权模型以及如何保护 Key Vault。
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 5b32e4897e718e0e411caf9ba76b036f1352bde0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715280"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault 安全性

使用 Azure Key Vault 可以保护云中的加密密钥和机密，例如证书、连接字符串和密码。 由于你要存储敏感数据和关键业务数据，因此需要采取措施来最大限度地提高保管库及其存储的数据的安全性。 本文介绍在设计 Azure Key Vault 安全性时应该了解的概念。

## <a name="identity-and-access-management"></a>标识和访问管理

在 Azure 订阅中创建密钥保管库时，该密钥保管库自动与订阅的 Azure AD 租户关联。 尝试管理或检索保管库中的内容的任何人必须已由 Azure AD 进行身份验证。

- 身份验证可确定调用方的身份。
- 授权可确定调用方能够执行的操作。 Key Vault 中的授权使用[基于角色的访问控制](../role-based-access-control/overview.md) (RBAC) 和 Azure Key Vault 访问策略的组合。

### <a name="access-model-overview"></a>访问模型概述

对保管库的访问是通过两个界面或平面发生的。 这些平面为管理平面和数据平面。

- 管理平面是管理 Key Vault 本身的位置，它是用来创建和删除保管库的界面。  你还可以读取 Key Vault 属性及管理访问策略。
- 数据平面用于处理 Key Vault 中存储的数据。  可以添加、删除和修改密钥、机密及证书。

若要在任一平面中访问 Key Vault，所有调用方（用户或应用程序）必须已经过身份验证且已获得授权。 对于身份验证，这两个平面都使用 Azure Active Directory (Azure AD)。 对于授权，管理平面使用基于角色的访问控制 (RBAC)，而数据平面使用密钥保管库访问策略。

对这两种平面使用单一身份验证机制模型具有多个优点：

- 组织可以集中控制对其组织中的所有密钥保管库的访问。
- 离职的用户会立即失去对组织中所有密钥保管库的访问权限。
- 组织可以通过 Azure AD 中的选项自定义身份验证（例如，启用多重身份验证以提高安全性）

### <a name="managing-administrative-access-to-key-vault"></a>管理对 Key Vault 的管理访问权限

在资源组中创建 Key Vault 时，可以使用 Azure AD 管理访问权限。 授予用户或组管理资源组中的密钥保管库的权限。 可以通过分配适当的 RBAC 角色在特定范围级别授予访问权限。 若要授予用户管理密钥保管库的访问权限，请为特定范围的用户分配预定义的 `key vault Contributor` 角色。 可以将以下范围级别分配给 RBAC 角色：

- **订阅**：在订阅级别分配的 RBAC 角色适用于该订阅中的所有资源组和资源。
- **资源组**：在资源组级别分配的 RBAC 角色适用于该资源组中的所有资源。
- **特定资源**：为特定资源分配的 RBAC 角色适用于该资源。 在这种情况下，资源是特定的密钥保管库。

有多种预定义角色。 如果预定义角色不符合需求，可以定义自己的角色。 有关详细信息，请参阅 [RBAC：内置角色](../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果用户具有密钥保管库管理平面的 `Contributor` 权限，则该用户可以通过设置密钥保管库访问策略来授予自己对数据平面的访问权限。 应严格控制对密钥保管库具有 `Contributor` 角色访问权限的用户。 请确保仅授权的人员才能访问和管理 Key Vault、密钥、机密和证书。

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>控制对 Key Vault 数据的访问

Key Vault 访问策略单独授予对密钥、机密或证书的权限。 可以仅授予用户对密钥的访问权限，而不授予对机密的访问权限。 密钥、机密或证书的访问权限在保管库级别进行管理。

> [!IMPORTANT]
> 密钥保管库访问策略不支持粒度、对象级别权限，例如特定的密钥、机密或证书。 如果授予某个用户创建和删除密钥的权限，该用户可以针对该密钥保管库中的所有密钥执行这些操作。

若要为密钥保管库设置访问策略，可以使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI 工具](../cli-install-nodejs.md)、[PowerShell](/powershell/azureps-cmdlets-docs) 或[密钥保管库管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)。

可以通过使用[适用于 Azure 密钥保管库的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)来限制数据平面访问权限。 可以配置[防火墙和虚拟网络规则](key-vault-network-security.md)以提供额外的安全层。

## <a name="network-access"></a>网络访问

可以通过指定哪些 IP 地址有权访问保管库，来降低保管库的风险。 通过 Azure Key Vault 的虚拟网络服务终结点可将访问限制为指定虚拟网络。 此外，还可通过这些终结点将访问限制为一系列 IPv4（Internet 协议版本 4）地址范围。 任何从外部连接到 Key Vault 的用户都无法访问这些资源。

防火墙规则生效后，仅当用户的请求来自允许的虚拟网络或 IPv4 地址范围时，他们才能从 Key Vault 读取数据。 从 Azure 门户访问 Key Vault 时，这同样适用。 虽然用户可从 Azure 门户浏览到 Key Vault，但如果其客户端计算机不在允许列表中，则可能无法列出密钥、机密或证书。 这也会影响其他 Azure 服务的 Key Vault 选取器。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看 Key Vault 列表，但不能查看列表密钥。

有关 Azure Key Vault 网络地址的详细信息，请查看 [Azure Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>监视

Key Vault 日志记录保存有关针对保管库执行的活动的信息。 Key Vault 将会记录：

- 所有经过身份验证的 REST API 请求，包括失败的请求
  - 针对 Key Vault 本身执行的操作。 这些操作包括创建、删除、设置访问策略，以及更新 Key Vault 属性（例如标记）。
  - 针对 Key Vault 中的密钥和机密执行的操作，包括：
    - 创建、修改或删除这些密钥或机密。
    - 签名、验证、加密、解密、包装和解包密钥、获取机密，以及列出密钥和机密（及其版本）。
- 导致出现 401 响应的未经身份验证的请求。 示例包括不包含持有者令牌、格式不正确或已过期，或者包含无效令牌的请求。

在执行 Key Vault 操作 10 分钟后，即可访问日志记录信息。 存储帐户中的日志完全由你管理。 

- 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
- 删除不想继续保留在存储帐户中的日志。

有关如何安全管理存储帐户的建议，请查看 [Azure 存储安全指南](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)
- [RBAC：内置角色](../role-based-access-control/built-in-roles.md)
- [Azure Key Vault 的虚拟网络服务终结点](key-vault-overview-vnet-service-endpoints.md)
