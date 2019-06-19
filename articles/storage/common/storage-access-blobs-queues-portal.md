---
title: 使用 Azure 门户访问 Blob 或队列数据 - Azure 存储
description: 使用 Azure 门户访问 Blob 或队列数据时，门户会在后台对 Azure 存储发出请求。 可以使用 Azure AD 帐户或存储帐户访问密钥对这些 Azure 存储请求进行身份验证和授权。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 00f34fa9a1932aebd467163e0ed7441c993387df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154012"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>使用 Azure 门户访问 Blob 或队列数据

使用 [Azure 门户](https://portal.azure.com)访问 Blob 或队列数据时，门户会在后台对 Azure 存储发出请求。 可以使用 Azure AD 帐户或存储帐户访问密钥对这些 Azure 存储请求进行身份验证和授权。 门户会指示使用的是哪种身份验证方法，如果你有相应的权限，则门户还允许在两种方法之间切换。  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>访问 Blob 或队列数据所需的权限

根据你在 Azure 门户中访问 Blob 或队列数据时进行身份验证的方式，需要拥有特定的权限。 在大多数情况下，这些权限是通过基于角色的访问控制 (RBAC) 提供的。 有关 RBAC 的详细信息，请参阅[什么是基于角色的访问控制 (RBAC)？](../../role-based-access-control/overview.md)。

### <a name="account-access-key"></a>帐户访问密钥

若要使用帐户访问密钥访问 Blob 和队列数据，必须拥有某种 RBAC 角色，此角色包含 RBAC 操作 **Microsoft.Storage/storageAccounts/listkeys/action**。 此 RBAC 角色可为内置角色，也可为自定义角色。 支持 **Microsoft.Storage/storageAccounts/listkeys/action** 的内置角色包括：

- Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure 资源管理器[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

尝试在 Azure 门户中访问 Blob 或队列数据时，门户首先会检查你是否拥有一个包含 **Microsoft.Storage/storageAccounts/listkeys/action** 的角色。 如果你拥有包含此操作的角色，则门户将使用帐户密钥来访问 Blob 和队列数据。 如果你不拥有包含此操作的角色，则门户会尝试使用你的 Azure AD 帐户访问数据。

> [!NOTE]
> 经典订阅管理员角色“服务管理员”和“共同管理员”具有 Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色的等效权限。 “所有者”角色包含所有操作，其中包括 **Microsoft.Storage/storageAccounts/listkeys/action**，因此，拥有其中一种管理角色的用户也可以使用帐户密钥访问 Blob 和队列数据。  有关详细信息，请参阅[经典订阅管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="azure-ad-account"></a>Azure AD 帐户

若要使用 Azure AD 帐户从 Azure 门户访问 Blob 或队列数据，必须符合以下条件：

- 至少拥有 Azure 资源管理器[读取者](../../role-based-access-control/built-in-roles.md#reader)角色，该角色的权限范围为存储帐户或更高级别。 “读取者”角色授予限制性最高的权限，但也接受可授予存储帐户管理资源访问权限的其他 Azure 资源管理器角色。 
- 拥有一个可提供 Blob 或队列数据访问权限的内置角色或自定义角色。

必须提供“读取者”角色分配或其他 Azure 资源管理器角色分配，使用户能够在 Azure 门户中查看和导航存储帐户管理资源。  授予 Blob 或队列数据访问权限的 RBAC 角色不会授予存储帐户管理资源访问权限。 若要在门户中访问 Blob 或队列数据，用户需要拥有导航存储帐户资源的权限。 有关此要求的详细信息，请参阅[分配“读取者”角色以访问门户](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)。

支持访问 Blob 或队列数据的内置角色包括：

- [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)：用于对 Azure Data Lake Storage Gen2 进行 POSIX 访问控制。
- [存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：对 Blob 的读取/写入/删除权限。
- [存储 Blob 数据读取者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)：对 Blob 的只读权限。
- [存储队列数据参与者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：对队列的读取/写入/删除权限。
- [存储队列数据读取者](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)：对队列的只读权限。
    
自定义角色能够支持内置角色所提供的相同权限的不同组合。 有关创建自定义 RBAC 角色的详细信息，请参阅 [Azure 资源的自定义角色](../../role-based-access-control/custom-roles.md)和[了解 Azure 资源的角色定义](../../role-based-access-control/role-definitions.md)。

> [!NOTE]
> 不支持使用经典订阅管理员角色列出队列。 若要列出队列，用户必须拥有 Azure 资源管理器“读取者”角色、“存储队列数据读取者”角色或“存储队列数据参与者”角色。   

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>在门户中导航到 Blob 或队列

若要在门户中查看 Blob 或队列数据，请导航到存储帐户的“概述”，然后单击“Blob”或“队列”对应的链接。    或者，可以在菜单中导航到“Blob 服务”和“队列服务”部分。   

![在 Azure 门户中导航到 Blob 或队列数据](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>确定当前的身份验证方法

导航到容器或队列时，Azure 门户会指示当前是使用帐户访问密钥还是 Azure AD 帐户进行身份验证。

本部分中的示例演示如何访问容器及其 Blob，但访问队列及其消息或列出队列时，门户会显示相同的消息。

### <a name="account-access-key"></a>帐户访问密钥

如果使用帐户访问密钥进行身份验证，则会在门户中看到“访问密钥”已指定为身份验证方法： 

![当前正在使用帐户密钥访问容器数据](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

若要改用 Azure AD 帐户，请单击图中突出显示的链接。 如果你通过拥有的 RBAC 角色获得了相应的权限，则可以继续访问。 但是，如果你缺少相应的权限，则会看到如下所示的错误消息：

![Azure AD 帐户不支持访问时显示的错误](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

请注意，如果你的 Azure AD 帐户缺少 Blob 查看权限，则列表中不会显示任何 Blob。 单击“切换为访问密钥”链接，以再次使用访问密钥进行身份验证。 

### <a name="azure-ad-account"></a>Azure AD 帐户

如果使用 Azure AD 帐户进行身份验证，则会在门户中看到“Azure AD 用户帐户”已指定为身份验证方法： 

![当前正在使用 Azure AD 帐户访问容器数据](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

若要改用帐户访问密钥，请单击图中突出显示的链接。 如果你有权访问帐户密钥，则可以继续访问。 但是，如果你缺少帐户密钥的访问权限，则会看到如下所示的错误消息：

![无权访问帐户密钥时显示的错误](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

请注意，如果你无权访问帐户密钥，则列表中不会显示任何 Blob。 单击“切换为 Azure AD 用户帐户”链接，以再次使用 Azure AD 帐户进行身份验证。 

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 验证对 Azure Blob 和队列的访问权限](storage-auth-aad.md)
- [在 Azure 门户中使用 RBAC 授予对 Azure 容器和队列的访问权限](storage-auth-aad-rbac-portal.md)
- [在 Azure CLI 中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-cli.md)
- [在 PowerShell 中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-powershell.md)
