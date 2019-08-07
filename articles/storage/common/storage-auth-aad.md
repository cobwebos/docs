---
title: 使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限 | Microsoft Docs
description: 使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/02/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f33193e3102afca73344fcd640d14d9af9c1d46e
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775343"
---
# <a name="authorize-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限

Azure 存储支持使用 Azure Active Directory (AD) 授予对 Blob 和队列存储的请求权限。 可以通过 Azure AD 使用基于角色的访问控制 (RBAC) 授予对服务主体的访问权限，该服务主体可能是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会返回 OAuth 2.0 令牌。 可以使用令牌对用户要求访问 Blob 或队列存储中资源的请求进行授权。

与共享密钥授权和共享访问签名 (SAS) 相比，使用 Azure AD 返回的 OAuth 2.0 令牌对用户或应用程序授权具有更高的安全性和易用性。 使用 Azure AD 时，不需将帐户访问密钥与代码存储在一起，因此没有潜在的安全漏洞风险。 虽然可以继续为应用程序使用共享密钥授权，但是，使用 Azure AD 不需要将帐户访问密钥与代码存储在一起。 也可以继续使用共享访问签名 (SAS) 授予对存储帐户中的资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄露的 SAS。 Microsoft 建议尽可能使用 Azure 存储应用程序 Azure AD 授权。

可以针对所有公共区域和国家/地区云的所有常规用途帐户和 Blob 存储帐户使用 Azure AD 进行授权。 仅通过 Azure 资源管理器部署模型创建的存储帐户支持 Azure AD 授权。 Azure 表存储不支持带有 Azure AD 的授权。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>适用于 Blob 和队列的 Azure AD 概述

当某个安全主体（用户、组或应用程序）尝试访问 Blob 或队列资源时，除非该 Blob 可供匿名访问，否则必须为请求授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。 接下来，将该令牌作为请求的一部分传递给 Blob 或队列服务，服务将使用它来授权访问指定的资源。

身份验证步骤要求应用程序在运行时请求 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体 (例如 Azure VM、虚拟机规模集或 Azure Functions 应用) 中运行, 则它可以使用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)来访问 blob 或队列。 若要了解如何将托管标识发出的请求授权给 Azure Blob 或队列服务, 请参阅[使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限](storage-auth-aad-msi.md)。

授权步骤需要将一个或多个 RBAC 角色分配给安全主体。 Azure 存储提供 RBAC 角色，这些角色涵盖了针对 Blob 和队列数据的通用权限集。 分配给安全主体的角色确定了该主体拥有的权限。 若要详细了解如何为 Azure 存储分配 RBAC 角色，请参阅[通过 RBAC 管理存储数据访问权限](storage-auth-aad-rbac.md)。

向 Azure Blob 或队列服务发出请求的本机应用程序和 Web 应用程序也可以使用 Azure AD 进行访问授权。 若要了解如何请求访问令牌并使用它来授权对 Blob 或队列数据的请求，请参阅[从 Azure 存储应用程序使用 Azure AD 授予对 Azure 存储的访问权限](storage-auth-aad-app.md)。

## <a name="assigning-rbac-roles-for-access-rights"></a>分配 RBAC 角色以授予访问权限

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储定义了一组内置的 RBAC 角色，它们包含用于访问 Blob 和队列数据的通用权限集。 还可以定义自定义角色来访问 Blob 和队列数据。

将 RBAC 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体或[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>适用于 Blob 和队列的内置 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

若要了解如何将内置 RBAC 角色分配给安全主体，请参阅以下文章之一：

- [在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)
- [在 Azure CLI 中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-cli.md)
- [在 PowerShell 中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-powershell.md)

有关如何为 Azure 存储定义内置角色的详细信息，请参阅[了解角色定义](../../role-based-access-control/role-definitions.md#management-and-data-operations)。 若要了解如何创建自定义 RBAC 角色，请参阅[针对 Azure 基于角色的访问控制创建自定义角色](../../role-based-access-control/custom-roles.md)。

### <a name="access-permissions-for-data-operations"></a>数据操作访问权限

有关调用特定 Blob 或队列服务操作所需的权限的详细信息，请参阅[用于调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

## <a name="resource-scope"></a>资源范围

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>使用 Azure AD 帐户访问数据

可以使用用户的 Azure AD 帐户或使用帐户访问密钥（共享密钥授权）来授权通过 Azure 门户、PowerShell 或 Azure CLI 访问 Blob 或队列数据。

### <a name="data-access-from-the-azure-portal"></a>通过 Azure 门户访问数据

Azure 门户可以使用 Azure AD 帐户或帐户访问密钥来访问 Azure 存储帐户中的 Blob 和队列数据。 Azure 门户使用哪种授权方案取决于分配给你的 RBAC 角色。

当你尝试访问 Blob 或队列数据时，Azure 门户首先会检查你是否拥有一个包含 **Microsoft.Storage/storageAccounts/listkeys/action** 的 RBAC 角色。 如果你拥有包含此操作的角色，则 Azure 门户将使用帐户密钥通过共享密钥授权来访问 Blob 和队列数据。 如果你不拥有包含此操作的角色，则 Azure 门户会尝试使用你的 Azure AD 帐户访问数据。

若要使用 Azure AD 帐户通过 Azure 门户访问 Blob 或队列数据，需要拥有访问 Blob 和队列数据的权限，另外还需要拥有在 Azure 门户中浏览存储帐户资源的权限。 Azure 存储提供的内置角色授予对 Blob 和队列资源的访问权限，但不授予对存储帐户资源的权限。 出于此原因，访问门户还需要分配范围为存储帐户或更高级别的 Azure 资源管理器角色，例如[读取者](../../role-based-access-control/built-in-roles.md#reader)角色。 “读取者”角色授予限制性最高的权限，但也接受可授予存储帐户管理资源访问权限的其他 Azure 资源管理器角色。 若要详细了解如何分配权限，使用户能够使用 Azure AD 帐户在 Azure 门户中访问数据，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)。

当你导航到容器或队列时，Azure 门户会指示当前正在使用哪种授权方案。 有关在门户中访问数据的详细信息，请参阅[使用 Azure 门户访问 Blob 或队列数据](storage-access-blobs-queues-portal.md)。

### <a name="data-access-from-powershell-or-azure-cli"></a>通过 PowerShell 或 Azure CLI 访问数据

Azure CLI 和 PowerShell 支持使用 Azure AD 凭据登录。 登录后，会话将在这些凭据下运行。 有关详细信息，请参阅[使用 Azure AD 凭据运行 Azure CLI 或 PowerShell 命令以访问 Blob 或队列数据](storage-auth-aad-script.md)。

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>针对 Azure 文件的 SMB Azure AD 授权

Azure 文件支持通过 SMB Azure AD 仅限已加入域的 Vm (预览版) 的授权。 若要了解如何在 Azure 文件的 SMB 上使用 Azure AD, 请参阅[Azure 文件的 smb 的 Azure Active Directory 授权概述 (预览版)](../files/storage-files-active-directory-overview.md)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限](storage-auth-aad-msi.md)
- [从应用程序中使用 Azure Active Directory 进行身份验证以访问 blob 和队列](storage-auth-aad-app.md)
- [Azure 存储对基于 Azure Active Directory 的访问控制的支持现已正式发布](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
