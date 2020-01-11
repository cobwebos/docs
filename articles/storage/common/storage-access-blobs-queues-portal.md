---
title: 使用 Azure 门户访问 blob 或队列数据
titleSuffix: Azure Storage
description: 当你使用 Azure 门户访问 blob 或队列数据时，门户会向 Azure 存储器发出请求。 可以使用 Azure AD 帐户或存储帐户访问密钥对 Azure 存储请求进行身份验证和授权。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866897"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>使用 Azure 门户访问 blob 或队列数据

当你使用[Azure 门户](https://portal.azure.com)访问 blob 或队列数据时，门户会向 Azure 存储器发出请求。 可以使用 Azure AD 帐户或存储帐户访问密钥来授权对 Azure 存储的请求。 门户指示你使用的是哪种方法，如果你具有适当的权限，则可以在两者之间进行切换。  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>访问 blob 或队列数据所需的权限

你需要特定的权限，具体取决于你希望如何授权访问 Azure 门户中的 blob 或队列数据。 在大多数情况下，这些权限是通过基于角色的访问控制（RBAC）来提供的。 有关 RBAC 的详细信息，请参阅[什么是基于角色的访问控制（RBAC）？](../../role-based-access-control/overview.md)。

### <a name="account-access-key"></a>帐户访问密钥

若要使用帐户访问密钥访问 blob 和队列数据，必须为你分配一个 RBAC 角色，其中包括 RBAC 操作 " **storageAccounts//listkeys/action**"。 此 RBAC 角色可以是内置角色或自定义角色。 支持 storageAccounts/ **listkeys/操作**的内置角色包括：

- Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色
- Azure 资源管理器[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色
- [存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)角色

尝试访问 Azure 门户中的 blob 或队列数据时，门户首先会检查是否已将角色分配给**storageAccounts//listkeys/action**。 如果已分配有此操作的角色，门户将使用帐户密钥来访问 blob 和队列数据。 如果尚未为此操作分配角色，门户会尝试使用 Azure AD 帐户访问数据。

> [!NOTE]
> 经典订阅管理员角色服务管理员和共同管理员包括 Azure 资源管理器[所有者](../../role-based-access-control/built-in-roles.md#owner)角色的等效项。 **所有者**角色包括所有操作，包括 storageAccounts/ **listkeys/操作**，因此具有以下管理角色之一的用户也可以使用帐户密钥访问 blob 和队列数据。 有关详细信息，请参阅[经典订阅管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="azure-ad-account"></a>Azure AD 帐户

若要使用你的 Azure AD 帐户从 Azure 门户访问 blob 或队列数据，以下两个语句都必须为 true：

- 已将 Azure 资源管理器[Reader](../../role-based-access-control/built-in-roles.md#reader)角色至少分配给存储帐户级别或更高级别。 "**读取**者" 角色授予最受限制的权限，但另一个授予对存储帐户管理资源的访问权限的 Azure 资源管理器角色也是可接受的。
- 已为你分配了可访问 blob 或队列数据的内置或自定义角色。

"**读者**" 角色分配或其他 Azure 资源管理器角色分配是必需的，以便用户可以在 Azure 门户中查看和导航存储帐户管理资源。 授权访问 blob 或队列数据的 RBAC 角色不会授予对存储帐户管理资源的访问权限。 若要在门户中访问 blob 或队列数据，用户需要具有导航存储帐户资源的权限。 有关此要求的详细信息，请参阅为[门户访问分配读取者角色](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)。

支持访问 blob 或队列数据的内置角色包括：

- [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)：对于 AZURE DATA LAKE STORAGE GEN2 的 POSIX 访问控制。
- [存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)：对 blob 的读取/写入/删除权限。
- [存储 Blob 数据读取器](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)：对 blob 的只读权限。
- [存储队列数据参与者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：队列的读取/写入/删除权限。
- [存储队列数据读取器](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)：队列的只读权限。
    
自定义角色可支持内置角色提供的相同权限的不同组合。 有关创建自定义 RBAC 角色的详细信息，请参阅[azure 资源的自定义角色](../../role-based-access-control/custom-roles.md)和[了解 azure 资源的角色定义](../../role-based-access-control/role-definitions.md)。

不支持使用经典订阅管理员角色列出队列。 若要列出队列，用户必须分配有 Azure 资源管理器**读取**者角色、**存储队列数据读取器**角色或**存储队列数据参与者**角色。

> [!IMPORTANT]
> Azure 门户中存储资源管理器的预览版本不支持使用 Azure AD 凭据来查看和修改 blob 或队列数据。 Azure 门户中的存储资源管理器始终使用帐户密钥来访问数据。 若要在 Azure 门户中使用存储资源管理器，必须为用户分配包含 storageAccounts/ **/listkeys/action**的角色。

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>在门户中导航到 blob 或队列

若要在门户中查看 blob 或队列数据，请导航到存储帐户的 "**概述**"，然后单击 " **blob** " 或 "**队列**" 的链接。 或者，可以导航到 " **Blob 服务**"，然后在菜单中**队列服务**"部分。 

![导航到 Azure 门户中的 blob 或队列数据](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>确定当前身份验证方法

导航到容器或队列时，Azure 门户将指示当前是否正在使用帐户访问密钥或 Azure AD 帐户进行身份验证。

本节中的示例演示如何访问容器及其 blob，但在访问队列及其消息或列出队列时，门户会显示相同的消息。

### <a name="account-access-key"></a>帐户访问密钥

如果要使用帐户访问密钥进行身份验证，你会在门户中看到指定为身份验证方法的**访问密钥**：

![当前正在用帐户密钥访问容器数据](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

若要切换到使用 Azure AD 帐户，请单击图像中突出显示的链接。 如果你通过分配给你的 RBAC 角色拥有适当的权限，你将能够继续操作。 但是，如果您没有适当的权限，您将看到如下错误消息：

![如果 Azure AD 帐户不支持访问，则显示错误](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

请注意，如果 Azure AD 帐户无权查看任何 blob，则不会在列表中显示。 单击 "**切换到访问密钥**" 链接，再次使用访问密钥进行身份验证。

### <a name="azure-ad-account"></a>Azure AD 帐户

如果你正在使用 Azure AD 帐户进行身份验证，你将看到在门户中将**Azure AD 用户帐户**指定为身份验证方法：

![当前正在访问 Azure AD 帐户的容器数据](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

若要切换到使用帐户访问密钥，请单击图像中突出显示的链接。 如果有权访问帐户密钥，可以继续操作。 但是，如果你没有帐户密钥的访问权限，你将看到如下错误消息：

![如果你没有帐户密钥的访问权限，则显示错误](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

请注意，如果您无权访问帐户密钥，则不会在列表中显示任何 blob。 单击 "**切换到 Azure AD 用户帐户**" 链接，再次使用 Azure AD 帐户进行身份验证。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 验证对 Azure blob 和队列的访问权限](storage-auth-aad.md)
- [在 Azure 门户中使用 RBAC 授予对 Azure 容器和队列的访问权限](storage-auth-aad-rbac-portal.md)
- [通过 Azure CLI 使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-cli.md)
- [通过 PowerShell 使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-powershell.md)
