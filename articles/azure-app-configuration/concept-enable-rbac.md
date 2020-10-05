---
title: 使用 Azure Active Directory 授予对 Azure 应用程序配置的访问权限
description: 启用 Azure RBAC 以授权访问你的 Azure 应用配置实例
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 7e7b524f125eda406adc9be2300c94e5944e1819
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715955"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>使用 Azure Active Directory 授予对 Azure 应用程序配置的访问权限
除了使用基于哈希的消息验证码 (HMAC)，Azure 应用程序配置还支持使用 Azure Active Directory (Azure AD) 授权对应用程序配置实例的请求。  Azure AD 允许使用 azure RBAC)  (Azure 基于角色的访问控制向安全主体授予权限。  安全主体可以是用户、[托管标识](../active-directory/managed-identities-azure-resources/overview.md)或[应用程序服务主体](../active-directory/develop/app-objects-and-service-principals.md)。  若要了解有关角色和角色分配的详细信息，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
安全主体发出的用于访问应用程序配置资源的请求必须获得授权。 使用 Azure AD 访问资源的过程分为两个步骤：
1. 对安全主体的标识进行身份验证，并返回 OAuth 2.0 令牌。  请求令牌的资源名称为 `https://login.microsoftonline.com/{tenantID}`，其中 `{tenantID}` 与服务主体所属的 Azure Active Directory 租户 ID 匹配。
2. 令牌作为请求的一部分传递到应用程序配置服务，以授予对指定资源的访问权限。

身份验证步骤要求应用程序请求在运行时包含 OAuth 2.0 访问令牌。  如果应用程序在 Azure 实体（例如 Azure Functions 应用、Azure Web 应用或 Azure VM）中运行，则它可以使用托管标识来访问资源。  若要了解如何对托管标识向 Azure 应用程序配置发出的请求进行身份验证，请参阅[使用 Azure Active Directory 和 Azure 资源的托管标识对访问 Azure 应用配置资源进行身份验证](howto-integrate-azure-managed-service-identity.md)。

授权步骤要求将一个或多个 Azure 角色分配给安全主体。 Azure 应用程序配置提供了 Azure 角色，这些角色包含应用程序配置资源的权限集。 分配给安全主体的角色确定提供给主体的权限。 有关 Azure 角色的详细信息，请参阅 [Azure 应用程序配置的 Azure 内置角色](#azure-built-in-roles-for-azure-app-configuration)。 

## <a name="assign-azure-roles-for-access-rights"></a>分配 Azure 角色以授予访问权限
Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 授予对受保护资源的访问权限。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 访问范围仅限于应用程序配置资源。 Azure AD 安全主体可以是用户、应用程序服务主体或 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="azure-built-in-roles-for-azure-app-configuration"></a>Azure 应用程序配置的 Azure 内置角色
Azure 提供下列 Azure 内置角色，用于使用 Azure AD 和 OAuth 授予对应用程序配置数据的访问权限：

- 应用程序配置数据所有者：使用此角色授予对应用程序配置数据的读取/写入/删除访问权限。 这不会授予对应用程序配置资源的访问权限。
- 应用程序配置数据读取者：使用此角色授予对应用程序配置数据的读取访问权限。 这不会授予对应用程序配置资源的访问权限。
- **参与者**：使用此角色管理应用程序配置资源。 虽然可以使用访问密钥访问应用程序配置数据，但此角色不会使用 Azure AD 授予对数据的直接访问权限。
- **读者**：使用此角色授予对应用程序配置资源的读取访问权限。 这不会授予对资源的访问密钥的访问权限，也不会授予对存储在应用程序配置中的数据的访问权限。

> [!NOTE]
> 目前，Azure 门户和 CLI 仅支持通过 HMAC 身份验证访问应用程序配置数据。 不支持 Azure AD 身份验证。 因此，Azure 门户和 CLI 的用户需要“参与者”角色才能检索应用程序配置资源的访问密钥。 授予“应用程序配置数据读取者”或“应用程序配置数据所有者”角色对于通过门户和 CLI 访问不产生任何影响 。

## <a name="next-steps"></a>后续步骤
了解有关使用[托管标识](howto-integrate-azure-managed-service-identity.md)管理应用程序配置服务的详细信息。
