---
title: 访问 Azure blob 和队列使用 Azure Active Directory 进行身份验证 |Microsoft Docs
description: 访问 Azure blob 和队列使用 Azure Active Directory 进行身份验证。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 08fdc20df70e4a97dd0cb99468354ef2b5c51f2b
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447037"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>访问 Azure blob 和队列使用 Azure Active Directory 进行身份验证

Azure 存储支持为 Blob 和队列服务使用 Azure Active Directory (AD) 进行身份验证和授权。 通过 Azure AD，可以使用基于角色的访问控制 (RBAC) 向用户、组或应用程序服务主体授予访问权限。 

与其他授权方式相比，使用 Azure AD 凭据对用户或应用程序进行身份验证可以提供优越的安全性和易用性。 虽然可以继续为应用程序使用共享密钥授权，但是，使用 Azure AD 不需要将帐户访问密钥与代码存储在一起。 也可以继续使用共享访问签名 (SAS) 授予对存储帐户中的资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄露的 SAS。 Microsoft 建议尽量对 Azure 存储应用程序使用 Azure AD 身份验证。

身份验证和授权与 Azure AD 凭据是适用于所有常规用途 v2、 常规用途 v1 和 Blob 存储帐户中的所有公共区域。 只有存储帐户使用 Azure 资源管理器部署模型支持创建 Azure AD 授权。

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 和队列的 Azure AD 的概述

将 Azure AD 与 Azure 存储集成的第一步是为服务主体（用户、组或应用程序服务主体）或 Azure 资源的托管标识分配对存储数据的 RBAC 角色。 RBAC 角色包含针对容器和队列的共有权限集。 若要了解有关 Azure 存储的分配 RBAC 角色的详细信息，请参阅[存储的数据使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。

若要使用 Azure AD 来授权访问应用程序中的存储资源，则需要通过代码请求一个 OAuth 2.0 访问令牌。 若要了解如何请求访问令牌并使用它来授权对 Azure 存储请求，请参阅[使用从 Azure 存储应用程序的 Azure AD 进行身份验证](storage-auth-aad-app.md)。 如果使用托管的标识，请参阅[blob 和队列使用 Azure 进行身份验证访问 Azure 资源的托管标识](storage-auth-aad-msi.md)。

Azure CLI 和 PowerShell 现在支持使用 Azure AD 标识创建日志记录。 使用 Azure AD 标识登录后，你的会话在该标识下运行。 若要了解详细信息，请参阅[使用 Azure AD 标识访问 Azure 存储使用 CLI 或 PowerShell](storage-auth-aad-script.md)。

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 和队列的 RBAC 角色

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储定义了一组内置的 RBAC 角色，它们包含用于访问容器或队列的通用权限集。 

RBAC 角色分配到 Azure AD 安全主体，Azure 授予访问这些资源时为该安全主体。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可能是用户、 组、 应用程序服务主体，或[管理 Azure 资源的标识](../../active-directory/managed-identities-azure-resources/overview.md)。

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

若要了解如何分配内置的 RBAC 的 Azure 存储资源，请参阅以下主题之一：

- [授予对 Azure blob 和队列数据使用 RBAC 在 Azure 门户中访问权限](storage-auth-aad-rbac-portal.md)
- [授予对 Azure blob 和队列数据与使用 Azure CLI 的 RBAC 访问权限](storage-auth-aad-rbac-cli.md)
- [授予对 Azure blob 和队列数据与使用 PowerShell 的 RBAC 访问权限](storage-auth-aad-rbac-powershell.md)

### <a name="access-permissions-granted-by-rbac-roles"></a>通过 RBAC 角色授予访问权限 

有关调用 Azure 存储操作所需的权限的详细信息，请参阅[用于调用 REST 操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。

## <a name="next-steps"></a>后续步骤

- [从应用程序中使用 Azure Active Directory 进行身份验证以访问 blob 和队列](storage-auth-aad-app.md)
- [进行对 blob 和队列管理的标识访问 Azure 资源的身份验证](storage-auth-aad-msi.md)
- Azure 文件仅支持使用 Azure AD 通过 SMB 进行身份验证，以用于加入域的 VM（预览版）。 若要了解有关在 Azure 文件中通过 SMB 使用 Azure AD 的信息，请参阅[通过 SMB 对 Azure 文件进行 Azure Active Directory身份验证概述（预览版）](../files/storage-files-active-directory-overview.md)。