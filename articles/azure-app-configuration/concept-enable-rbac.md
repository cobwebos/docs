---
title: 使用 Azure 活动目录授权访问 Azure 应用配置
description: 启用 RBAC 授权访问 Azure 应用配置实例
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472617"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>使用 Azure 活动目录授权访问 Azure 应用配置
Azure 应用配置支持使用 Azure 活动目录 （Azure AD） 授权对应用配置实例的请求。  Azure AD 允许您使用基于角色的访问控制 （RBAC） 向安全主体授予权限。  安全主体可以是用户或[应用程序服务主体](../active-directory/develop/app-objects-and-service-principals.md)。  要了解有关角色和角色分配的更多内容，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
必须授权安全主体（用户或应用程序）请求访问应用配置资源。  使用 Azure AD 是，访问资源的过程包括两个步骤。
1. 安全主体的标识经过身份验证，并返回 OAuth 2.0 令牌。  请求令牌的资源名称是`https://login.microsoftonline.com/{tenantID}`与服务主体所属`{tenantID}`的 Azure 活动目录租户 ID 匹配的位置。
2. 令牌作为请求的一部分传递给应用配置服务，以授权对指定资源的访问。

身份验证步骤要求应用程序请求包含在运行时使用的 OAuth 2.0 访问令牌。  如果应用程序在 Azure 实体（如 Azure 函数应用、Azure Web 应用或 Azure VM）中运行，则可以使用托管标识访问资源。  要了解如何对托管标识对 Azure 应用配置发出的请求进行身份验证，请参阅[使用 Azure 活动目录和 Azure 资源的托管标识对 Azure 应用配置资源进行身份验证访问](howto-integrate-azure-managed-service-identity.md)。

授权步骤需要将一个或多个 RBAC 角色分配给安全主体。 Azure 应用配置提供 RBAC 角色，这些角色包含应用配置资源的权限集。 分配给安全主体的角色确定向主体提供的权限。 有关 RBAC 角色的详细信息，请参阅[Azure 应用配置的内置 RBAC 角色](#built-in-rbac-roles-for-azure-app-configuration)。 

## <a name="assign-rbac-roles-for-access-rights"></a>分配 RBAC 角色以授予访问权限
Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 授权访问受保护的资源。

将 RBAC 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 访问范围为应用配置资源。 Azure AD 安全主体可以是用户或应用程序服务主体，也可以是 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Azure 应用配置的内置 RBAC 角色
Azure 提供了以下内置 RBAC 角色，用于使用 Azure AD 和 Auth 授权访问应用配置数据：

- Azure 应用配置数据所有者：使用此角色授予对应用配置资源的读/写访问权限。
- Azure 应用配置数据读取器：使用此角色授予对应用配置资源的读取访问权限。
- 参与者：使用此角色授予管理员对服务的访问权限，而无需授予对应用配置实例中存储的数据的访问权限。

## <a name="next-steps"></a>后续步骤
详细了解如何使用[托管标识](howto-integrate-azure-managed-service-identity.md)来管理应用配置服务。