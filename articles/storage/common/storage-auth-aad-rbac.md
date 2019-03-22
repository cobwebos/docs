---
title: 使用 Azure 门户来管理对容器和队列使用 RBAC （预览版）-Azure 存储的 Azure AD 访问权限
description: 使用 Azure 门户中基于角色的访问控制 (RBAC) 授予对容器和对安全主体的队列访问权限。 Azure 存储支持通过 Azure AD 进行身份验证的内置和自定义 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: a38cce7d15b4796bb66803d06f3589569985cb37
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319493"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal-preview"></a>授予对 Azure 容器和队列使用 RBAC （预览版） 在 Azure 门户中的访问权限

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储定义了一组内置的 RBAC 角色，它们包含用于访问容器或队列的通用权限集。 

RBAC 角色分配到 Azure AD 安全主体，Azure 授予访问这些资源时为该安全主体。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可能是用户、 组、 应用程序服务主体，或[管理 Azure 资源的标识](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介绍如何使用 Azure 门户来分配 RBAC 角色。 Azure 门户提供了一个简单的界面，用于分配 RBAC 角色和管理存储资源的访问权限。 你还可以分配 RBAC 角色，以便使用 Azure 命令行工具或 Azure 存储管理 Api 的 blob 和队列资源。 有关存储资源的 RBAC 角色的详细信息，请参阅[进行身份验证访问 Azure blob 和队列使用 Azure Active Directory （预览版）](storage-auth-aad.md)。 

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 和队列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>确定资源范围 

将 RBAC 角色分配给安全主体之前，确定安全主体应具有的访问作用的域。 最佳做法要求，最好是始终授予仅尽可能小的范围。

下面列出的您可以将访问权限限制为 Azure blob 和队列资源，最小范围的开始的级别：

- **为单个容器中。** 在此作用域的安全主体有权访问所有的容器，以及容器属性和元数据中的 blob。
- **单个队列。** 在此作用域的安全主体有权访问队列，以及队列属性和元数据中的消息。
- **存储帐户中。** 在此作用域，安全主体具有对所有容器和其 blob，或对所有队列和其消息的访问权限。
- **资源组中。** 在此作用域的安全主体有权访问的所有容器或中的所有存储帐户的资源组中的队列。
- **订阅。** 在此作用域的安全主体有权访问的所有容器或队列中的所有存储帐户中的所有订阅中资源组。

在确定所需的范围的角色分配后，导航到 Azure 门户中的相应资源。 显示**访问控制 (IAM)** 设置对于资源，并按照后续部分中的说明，用于管理角色分配。

## <a name="assign-rbac-roles-using-the-azure-portal"></a>分配 RBAC 角色使用 Azure 门户

授予对使用 Azure 门户中的 Azure AD 凭据的 blob 和队列资源的访问涉及以下步骤： 

1. 分配相应的 Azure 存储 RBAC 角色，授予对容器或队列访问权限。 读取访问权限，将分配**Blob 数据读取器 （预览版）** 或**队列数据读取器 （预览版）** 角色。 对于读取、 写入和删除访问权限，将分配**Blob 数据参与者 （预览）** 或**队列数据参与者 （预览）** 角色。 此外可以分配自定义角色。

1. 分配 Azure 资源管理器[读取器](../../role-based-access-control/built-in-roles.md#reader)角色的用户需要访问容器或通过使用其 Azure AD 凭据在 Azure 门户的队列。 

以下部分介绍上述每个步骤中更多详细信息。

### <a name="assign-a-built-in-rbac-role"></a>将内置的 RBAC 角色分配

将角色分配给安全主体之前，请务必考虑你将向授予的权限的作用域。 审阅[确定资源范围](#determine-resource-scope)部分，确定合适的作用域。

此处所示的过程将分配限定于容器的角色，但你可以按照相同的步骤分配限定于队列的角色： 

1. 在 [Azure 门户](https://portal.azure.com)中，导航到存储帐户并显示该帐户的“概述”。
1. 在“服务”下选择“Blob”。 
1. 找到要针对其分配角色的容器，并显示该容器的设置。 
1. 选择“访问控制(IAM)”以显示容器的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。

    ![显示容器的访问控制设置的屏幕截图](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. 单击“添加角色分配”按钮以添加一个新角色。
1. 在中**添加的角色分配**窗口中，选择你想要分配的 Azure 存储角色。 然后通过搜索来查找你想要将该角色分配的安全主体。

    ![显示如何分配 RBAC 角色的屏幕截图](media/storage-auth-aad-rbac/add-rbac-role.png)

1. 单击“ **保存**”。 分配有该角色的标识列出在该角色下。 例如，下图显示添加的用户现在对名为 *sample-container* 的容器中的数据具有读取权限。

    ![分配到角色的用户的屏幕截图显示列表](media/storage-auth-aad-rbac/container-scoped-role.png)

可以遵循类似的步骤来将分配到存储帐户、 资源组或订阅作用域的角色。

> [!NOTE]
> 作为 Azure 存储帐户的所有者，系统不会自动向你分配数据访问权限。 你必须为自己显式分配一个用于 Azure 存储的 RBAC 角色。 可以在订阅、资源组、存储帐户、容器或队列级别分配它。
> 
> 无法分配作用域容器或队列，如果存储帐户已启用的分层命名空间的角色。

### <a name="assign-the-reader-role-for-portal-access"></a>将门户访问的读取者角色分配

内置或自定义角色的 Azure 存储分配给安全主体后，将向授予该安全主体的权限在您的存储帐户中执行数据操作的操作。 内置**数据读取器**角色提供对容器或队列中的数据的读取的权限中时，内置**数据参与者**角色提供读取、 写入和删除到容器的权限或队列。 指定的资源权限的作用域。  

例如，如果你将分配**存储 Blob 数据参与者 （预览版）** 用户 Mary 在名为的容器级别的角色**示例容器**、 然后 Mary 授予读取、 写入和删除的所有访问权限该容器中的 blob。

但是，如果希望在 Azure 门户中查看 blob Mary 然后**存储 Blob 数据参与者 （预览）** 角色本身将不会提供足够的权限才能查看它对该 blob 在门户中导航。 其他 Azure AD 权限才可在门户中导航和查看是否有可见的其他资源。

如果用户需要能够访问在 Azure 门户中的 blob，然后将其分配一个附加的 RBAC 角色[读取器](../../role-based-access-control/built-in-roles.md#reader)角色，向这些用户在级别或更高版本的存储帐户。 **读取器**角色是 Azure 资源管理器角色，以允许用户以查看存储帐户资源，但不能修改它们。 它不提供 Azure 存储中的数据，但仅对帐户管理资源的读取的权限。

请按照以下步骤将分配**读取器**角色，以便用户可以从 Azure 门户访问 blob。 在此示例中，分配的作用域覆盖到的存储帐户：

1. 在 [Azure 门户](https://portal.azure.com)中导航到存储帐户。
1. 选择**访问控制 (IAM)** 显示存储帐户的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。
1. 在中**添加的角色分配**窗口中，选择**读取器**角色。 
1. 从**分配访问权限**下拉列表中，选择**Azure AD 用户、 组或服务主体**。
1. 通过搜索来查找你想要将角色分配的安全主体。
1. 保存角色分配。

> [!NOTE]
> 将读取器角色分配是只需将需要用来访问 blob 或队列使用 Azure 门户的用户。 

## <a name="next-steps"></a>后续步骤

- 若要详细了解 RBAC，请参阅[什么是基于角色的访问控制 (RBAC)？](../../role-based-access-control/overview.md)
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 RBAC 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-powershell.md)
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-rest.md)
- 若要了解如何从存储应用程序内授权访问容器和队列，请参阅[将 Azure AD 与 Azure 存储应用程序配合使用](storage-auth-aad-app.md)。
- 有关适用于 Azure 容器和队列的 Azure AD 集成的其他信息，请参阅 Azure 存储团队博客文章[宣布推出适用于 Azure 存储的 Azure AD 身份验证预览版](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。
