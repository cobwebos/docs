---
title: 使用 Active Directory 授予对 blob 和队列的访问权限
titleSuffix: Azure Storage
description: 使用 Azure Active Directory 授予对 Azure blob 和队列的访问权限。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0678d437a5c24b8193e7440a62445fb30ec97759
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460504"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>使用 Azure Active Directory 授予对 blob 和队列的访问权限

Azure 存储支持使用 Azure Active Directory （Azure AD）授权对 Blob 和队列存储的请求。 使用 Azure AD，你可以使用基于角色的访问控制（RBAC）向安全主体授予权限，这可能是用户、组或应用程序服务主体。 通过 Azure AD 对安全主体进行身份验证，以返回 OAuth 2.0 令牌。 然后，令牌可用于对 Blob 或队列存储的请求进行授权。

使用 Azure AD 对 Azure 存储的请求进行授权，可通过共享密钥授权提供更高的安全性和易用性。 Microsoft 建议尽可能将 Azure AD 授权与 blob 和队列应用程序一起使用，以最大程度地减少共享密钥中固有的潜在安全漏洞。

使用 Azure AD 的授权适用于所有公共区域和全国云中的所有常规用途和 Blob 存储帐户。 只有用 Azure 资源管理器部署模型创建的存储帐户才支持 Azure AD 授权。

Blob 存储还支持创建使用 Azure AD 凭据签名的共享访问签名（SAS）。 有关详细信息，请参阅[使用共享访问签名授予对数据的有限访问权限](storage-sas-overview.md)。

对于已加入域的 Vm，Azure 文件仅支持通过 SMB Azure AD 的授权。 若要了解如何在 Azure 文件的 SMB 上使用 Azure AD，请参阅[Azure 文件的 smb 的 Azure Active Directory 授权概述](../files/storage-files-active-directory-overview.md)。

Azure 表存储不支持带有 Azure AD 的授权。 使用共享密钥对表存储的请求进行授权。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 和队列 Azure AD 概述

当安全主体（用户、组或应用程序）尝试访问 blob 或队列资源时，必须授权该请求，除非它是可用于匿名访问的 blob。 使用 Azure AD，访问资源的过程分为两个步骤。 首先，对安全主体的身份进行身份验证，并返回 OAuth 2.0 令牌。 接下来，令牌作为请求的一部分传递到 Blob 或队列服务，并由服务用来授予对指定资源的访问权限。

身份验证步骤要求应用程序在运行时请求 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（例如 Azure VM、虚拟机规模集或 Azure Functions 应用）中运行，则它可以使用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)来访问 blob 或队列。 若要了解如何将托管标识发出的请求授权给 Azure Blob 或队列服务，请参阅[使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限](storage-auth-aad-msi.md)。

授权步骤要求向安全主体分配一个或多个 RBAC 角色。 Azure 存储提供了 RBAC 角色，其中包含 blob 和队列数据的公用权限集。 分配给安全主体的角色确定主体将具有的权限。 若要详细了解如何为 Azure 存储分配 RBAC 角色，请参阅[使用 RBAC 管理对存储数据的访问权限](storage-auth-aad-rbac.md)。

向 Azure Blob 或队列服务发出请求的本机应用程序和 web 应用程序也可以使用 Azure AD 授予访问权限。 若要了解如何请求访问令牌并使用它来授权对 blob 或队列数据的请求，请参阅[使用 Azure 存储应用程序中的 Azure AD 授予对 Azure 存储的访问权限](storage-auth-aad-app.md)。

## <a name="assign-rbac-roles-for-access-rights"></a>为访问权限分配 RBAC 角色

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储空间定义了一组内置的 RBAC 角色，这些角色包含用于访问 blob 和队列数据的公用权限集。 你还可以定义自定义角色，以便访问 blob 和队列数据。

将 RBAC 角色分配到 Azure AD 安全主体时，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体或[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Blob 和队列的内置 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

若要了解如何向安全主体分配内置 RBAC 角色，请参阅以下文章之一：

- [在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)
- [通过 Azure CLI 使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-cli.md)
- [通过 PowerShell 使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-powershell.md)

有关如何为 Azure 存储定义内置角色的详细信息，请参阅[了解角色定义](../../role-based-access-control/role-definitions.md#management-and-data-operations)。 有关创建自定义 RBAC 角色的详细信息，请参阅[为 Azure 基于角色的访问控制创建自定义角色](../../role-based-access-control/custom-roles.md)。

### <a name="access-permissions-for-data-operations"></a>数据操作的访问权限

有关调用特定 Blob 或队列服务操作所需的权限的详细信息，请参阅[调用 Blob 和队列数据操作的权限](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

## <a name="resource-scope"></a>资源作用域

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>使用 Azure AD 帐户访问数据

可以通过使用用户的 Azure AD 帐户或使用帐户访问密钥（共享密钥授权）来授权通过 Azure 门户、PowerShell 或 Azure CLI 访问 blob 或队列数据。

### <a name="data-access-from-the-azure-portal"></a>Azure 门户的数据访问

Azure 门户可以使用 Azure AD 帐户或帐户访问密钥访问 Azure 存储帐户中的 blob 和队列数据。 Azure 门户使用哪种授权方案取决于分配给你的 RBAC 角色。

尝试访问 blob 或队列数据时，Azure 门户首先会检查是否已向你分配了具有 storageAccounts/listkeys/ **action**的 RBAC 角色。 如果已通过此操作分配了角色，则 Azure 门户使用帐户密钥通过共享密钥授权访问 blob 和队列数据。 如果尚未为此操作分配角色，则 Azure 门户会尝试使用 Azure AD 帐户访问数据。

若要使用你的 Azure AD 帐户从 Azure 门户访问 blob 或队列数据，你需要访问 blob 和队列数据的权限，并且还需要在 Azure 门户的存储帐户资源中导航的权限。 Azure 存储提供的内置角色授予对 blob 和队列资源的访问权限，但不授予对存储帐户资源的权限。 出于此原因，访问门户还需要分配 Azure 资源管理器角色，如 "[读者](../../role-based-access-control/built-in-roles.md#reader)" 角色，其作用域为存储帐户或更高级别。 "**读取**者" 角色授予最受限制的权限，但另一个授予对存储帐户管理资源的访问权限的 Azure 资源管理器角色也是可接受的。 若要详细了解如何使用 Azure AD 帐户将 Azure 门户中的数据访问的权限分配给用户，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure blob 和队列数据的访问](storage-auth-aad-rbac-portal.md)权限。

Azure 门户指示导航到容器或队列时使用的是哪个授权方案。 有关门户中的数据访问的详细信息，请参阅[使用 Azure 门户访问 blob 或队列数据](storage-access-blobs-queues-portal.md)。

### <a name="data-access-from-powershell-or-azure-cli"></a>从 PowerShell 或 Azure CLI 进行数据访问

Azure CLI 和 PowerShell 支持通过 Azure AD 凭据进行登录。 登录后，你的会话将在这些凭据下运行。 若要了解详细信息，请参阅[使用 Azure AD 凭据运行 Azure CLI 或 PowerShell 命令以访问 blob 或队列数据](storage-auth-aad-script.md)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限](storage-auth-aad-msi.md)
- [使用应用程序 Azure Active Directory 授权访问 blob 和队列](storage-auth-aad-app.md)
- [Azure 存储对基于 Azure Active Directory 的访问控制的支持现已正式发布](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
