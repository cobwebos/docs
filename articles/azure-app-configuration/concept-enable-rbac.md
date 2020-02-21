---
title: 使用 Azure Active Directory 授予对 Azure 应用配置的访问权限
description: 启用 RBAC 以授权访问你的 Azure 应用配置实例
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472617"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>使用 Azure Active Directory 授予对 Azure 应用配置的访问权限
Azure 应用配置支持使用 Azure Active Directory （Azure AD）对应用配置实例的请求进行授权。  Azure AD 允许你使用基于角色的访问控制（RBAC）向安全主体授予权限。  安全主体可以是用户，也可以是[应用程序服务主体](../active-directory/develop/app-objects-and-service-principals.md)。  若要了解有关角色和角色分配的详细信息，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
安全主体（用户或应用程序）发出的用于访问应用配置资源的请求必须获得授权。  使用 Azure AD，访问资源的过程分为两个步骤。
1. 对安全主体的标识进行身份验证，并返回 OAuth 2.0 令牌。  请求令牌的资源名称为 `https://login.microsoftonline.com/{tenantID}`，其中 `{tenantID}` 与服务主体所属的 Azure Active Directory 租户 ID 匹配。
2. 令牌作为请求的一部分传递到应用配置服务，以授权访问指定的资源。

身份验证步骤要求应用程序请求在运行时包含 OAuth 2.0 访问令牌。  如果应用程序在 Azure 实体（例如 Azure Functions 应用、Azure Web 应用或 Azure VM）中运行，则它可以使用托管标识来访问资源。  若要了解如何对托管标识发出的请求进行身份验证以 Azure 应用配置，请参阅[使用 Azure 资源 Azure Active Directory 和托管标识对 Azure 应用配置资源的访问权限进行身份验证](howto-integrate-azure-managed-service-identity.md)。

授权步骤要求向安全主体分配一个或多个 RBAC 角色。 Azure 应用配置提供了包含应用配置资源的权限集的 RBAC 角色。 分配给安全主体的角色确定提供给主体的权限。 有关 RBAC 角色的详细信息，请参阅[Azure 应用配置的内置 RBAC 角色](#built-in-rbac-roles-for-azure-app-configuration)。 

## <a name="assign-rbac-roles-for-access-rights"></a>为访问权限分配 RBAC 角色
Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 授权访问受保护的资源。

将 RBAC 角色分配到 Azure AD 安全主体时，Azure 会向该安全主体授予对这些资源的访问权限。 访问作用于应用配置资源。 Azure AD 安全主体可以是用户或应用程序服务主体，也可以是[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Azure 应用配置的内置 RBAC 角色
Azure 提供下列内置 RBAC 角色，用于使用 Azure AD 和 OAuth 授权访问应用配置数据：

- Azure 应用配置数据所有者：使用此角色授予对应用配置资源的读/写访问权限。
- Azure 应用配置数据读取器：使用此角色授予对应用配置资源的读取权限。
- 参与者：使用此角色授予对服务的管理员访问权限，而不授予对存储在应用程序配置实例中的数据的访问权限。

## <a name="next-steps"></a>后续步骤
了解有关使用[托管标识](howto-integrate-azure-managed-service-identity.md)管理应用配置服务的详细信息。