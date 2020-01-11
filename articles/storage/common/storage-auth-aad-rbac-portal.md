---
title: 使用 Azure 门户为数据访问分配 RBAC 角色
titleSuffix: Azure Storage
description: 了解如何使用 Azure 门户向具有基于角色的访问控制（RBAC）的 Azure Active Directory 安全主体分配权限。 Azure 存储通过 Azure AD 支持用于身份验证的内置和自定义 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ec32990513d9199c4aaccf1bcfcbf76f348f877b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867499"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>使用 Azure 门户分配用于访问 blob 和队列数据的 RBAC 角色

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储空间定义了一组内置的 RBAC 角色，这些角色包含用于访问 blob 或队列数据的公用权限集。

将 RBAC 角色分配到 Azure AD 安全主体时，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体或[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介绍如何使用 Azure 门户分配 RBAC 角色。 Azure 门户提供了一个简单的接口，用于分配 RBAC 角色并管理对存储资源的访问。 你还可以使用 Azure 命令行工具或 Azure 存储管理 Api 为 blob 和队列资源分配 RBAC 角色。 有关存储资源的 RBAC 角色的详细信息，请参阅[使用 Azure Active Directory 验证对 Azure blob 和队列的访问权限](storage-auth-aad.md)。 

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 和队列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>确定资源范围 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 门户分配 RBAC 角色

确定角色分配的适当作用域后，请导航到 Azure 门户中的相应资源。 显示资源的 "**访问控制（IAM）** " 设置，并按照这些说明来管理角色分配：

1. 分配适当的 Azure 存储 RBAC 角色以授予对 Azure AD 安全主体的访问权限。

1. 使用 Azure 门户的 Azure AD 凭据将 Azure 资源管理器[读取](../../role-based-access-control/built-in-roles.md#reader)者角色分配给需要通过访问容器或队列的用户。 

以下部分更详细地介绍了其中的每个步骤。

> [!NOTE]
> 作为 Azure 存储帐户的所有者，系统不会自动向你分配数据访问权限。 你必须为自己显式分配一个用于 Azure 存储的 RBAC 角色。 可以在订阅、资源组、存储帐户、容器或队列级别分配它。
>
> 如果你的存储帐户已启用分层命名空间，则无法将作用域分配给容器或队列。

### <a name="assign-a-built-in-rbac-role"></a>分配内置 RBAC 角色

向安全主体分配角色之前，请务必考虑要授予的权限的范围。 查看[确定资源范围](#determine-resource-scope)部分以决定适当的作用域。

此处所示的过程将分配限定于容器的角色，但你可以按照相同的步骤分配限定于队列的角色： 

1. 在[Azure 门户](https://portal.azure.com)中，请切换到你的存储帐户并显示该帐户的**概述**。
1. 在“服务”下选择“Blob”。 
1. 找到要针对其分配角色的容器，并显示该容器的设置。 
1. 选择“访问控制(IAM)”以显示容器的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。

    ![显示容器访问控制设置的屏幕截图](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. 单击“添加角色分配”按钮以添加一个新角色。
1. 在 "**添加角色分配**" 窗口中，选择要分配的 Azure 存储角色。 然后搜索以查找要向其分配该角色的安全主体。

    ![显示如何分配 RBAC 角色的屏幕截图](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. 单击“ **保存**”。 分配有该角色的标识列出在该角色下。 例如，下图显示添加的用户现在对名为 *sample-container* 的容器中的数据具有读取权限。

    ![显示分配给角色的用户列表的屏幕截图](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

可以遵循类似的步骤来分配作用域为存储帐户、资源组或订阅的角色。

### <a name="assign-the-reader-role-for-portal-access"></a>为门户网站访问分配 "读取者" 角色

向安全主体分配 Azure 存储的内置或自定义角色时，你将为该安全主体授予对存储帐户中的数据执行操作的权限。 内置**数据读取器**角色为容器或队列中的数据提供读取权限，而内置的**数据参与者**角色提供对容器或队列的读取、写入和删除权限。 权限的作用域限定为指定的资源。  
例如，如果将**存储 Blob 数据参与者**角色分配给名为**sample**容器的容器级别的用户 Mary，则会向 Mary 授予对该容器中的所有 blob 的读取、写入和删除访问权限。

但是，如果 Mary 要查看 Azure 门户中的 blob，则 "**存储 Blob 数据参与者**" 角色本身将不会提供足够的权限，以便在门户中导航到该 blob，以便查看该 blob。 若要在门户中导航并查看此处可见的其他资源，需要其他 Azure AD 权限。

如果用户需要能够访问 Azure 门户中的 blob，请在存储帐户或更高级别为这些用户分配其他 RBAC 角色、"读取者" 角色、"[读取](../../role-based-access-control/built-in-roles.md#reader)者" 角色。 "**读取**者" 角色是一种 Azure 资源管理器角色，它允许用户查看存储帐户资源，但不能修改这些资源。 它不提供对 Azure 存储中的数据的读取权限，而只用于帐户管理资源。

按照以下步骤分配 "**读取**者" 角色，以便用户可以从 Azure 门户访问 blob。 在此示例中，分配的作用域为存储帐户：

1. 在 [Azure 门户](https://portal.azure.com)中导航到存储帐户。
1. 选择 "**访问控制（IAM）** " 可显示存储帐户的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。
1. 在 "**添加角色分配**" 窗口中，选择 "**读取**者" 角色。 
1. 从 "**将访问权限分配到**" 字段中，选择**Azure AD 用户、组或服务主体**。
1. 搜索以查找要向其分配角色的安全主体。
1. 保存角色分配。

只有需要使用 Azure 门户访问 blob 或队列的用户才能分配 "**读者**" 角色。

> [!IMPORTANT]
> Azure 门户中存储资源管理器的预览版本不支持使用 Azure AD 凭据来查看和修改 blob 或队列数据。 Azure 门户中的存储资源管理器始终使用帐户密钥来访问数据。 若要在 Azure 门户中使用存储资源管理器，必须为用户分配包含 storageAccounts/ **/listkeys/action**的角色。

## <a name="next-steps"></a>后续步骤

- 有关存储资源的 RBAC 角色的详细信息，请参阅[使用 Azure Active Directory 验证对 Azure blob 和队列的访问权限](storage-auth-aad.md)。 
- 若要详细了解 RBAC，请参阅[什么是基于角色的访问控制 (RBAC)？](../../role-based-access-control/overview.md)
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 RBAC 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-powershell.md)
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-rest.md)
- 若要了解如何从存储应用程序内授权访问容器和队列，请参阅[将 Azure AD 与 Azure 存储应用程序配合使用](storage-auth-aad-app.md)。
