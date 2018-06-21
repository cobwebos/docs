---
title: 使用 RBAC 管理对 Azure 存储容器和队列的访问权限（预览版）| Microsoft Docs
description: 使用基于角色的访问控制 (RBAC) 将用于访问 Azure 存储数据的角色分配给用户、组、应用程序服务主体或托管服务标识。 Azure 存储支持通过内置角色和自定义角色来访问容器和队列。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: tamram
ms.openlocfilehash: cb77bd4418e105c877202f0f1725350380ea2308
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659221"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>使用 RBAC 管理对 Azure 存储数据的访问权限（预览版）

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 授权访问受保护的资源。 Azure 存储定义了一组内置的 RBAC 角色，它们包含用于访问容器或队列的通用权限集。 在向 Azure AD 标识分配 RBAC 角色时，系统会根据指定的作用域，向该标识授予对这些资源的访问权限。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 可以使用 Azure 门户、Azure 命令行工具及 Azure 管理 API 分配对 Azure 存储资源的访问权限。 

Azure AD 标识可以是用户、组或应用程序服务主体，也可以是*托管服务标识*。 安全主体可以是用户、组或应用程序服务主体。 [托管服务标识](../../active-directory/managed-service-identity/overview.md)是一种自动托管标识，用于从 Azure 虚拟机、函数应用、虚拟机规模集等中运行的应用程序进行身份验证。 有关 Azure AD 中标识的概述，请参阅[了解 Azure 标识解决方案](https://docs.microsoft.com/en-us/azure/active-directory/understand-azure-identity-solutions)。

## <a name="rbac-roles-for-azure-storage"></a>Azure 存储的 RBAC 角色

Azure 存储同时支持内置和自定义 RBAC 角色。 Azure 存储提供以下内置 RBAC 角色用于 Azure AD：

- [存储 Blob 数据参与者(预览)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [存储 Blob 数据读取器(预览)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [存储队列数据参与者(预览)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [存储队列数据读取器(预览)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

有关如何为 Azure 存储定义内置角色的详细信息，请参阅[了解角色定义](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview)。

还可以定义用于容器和队列的自定义角色。 有关详细信息，请参阅[针对 Azure 基于角色的访问控制创建自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles.md)。 

> [!IMPORTANT]
> 此预览版仅用于非生产用途。 适用于 Azure 存储的 Azure AD 集成正式发布后，生产服务级别协议 (SLA) 方可使用。 如果你的方案尚不支持 Azure AD 集成，请继续使用应用程序中的共享密钥授权或 SAS 令牌。 有关该预览版的其他信息，请参阅[使用 Azure Active Directory 验证 Azure 存储访问权限（预览版）](storage-auth-aad.md)。
>
> 预览期间，RBAC 角色分配可能需要长达五分钟的时间进行传播。

## <a name="assign-a-role-to-a-security-principal"></a>向安全主体分配角色

向 Azure 标识分配 RBAC 角色，以授予对存储帐户中容器或队列的权限。 可以将角色分配范围限定于存储帐户或特定的容器或队列。 下表总结了由内置角色授予的访问权限，具体取决于作用域： 

|                                 |     Blob 数据参与者                                                 |     Blob 数据读取器                                                |     队列数据参与者                                  |     队列数据读取器                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    限定于订阅       |    对订阅中的所有容器和 blob 具有读/写访问权限       |    对订阅中的所有容器和 blob 具有读取访问权限       |    对订阅中的所有队列具有读/写访问权限       |    对订阅中的所有队列具有读取访问权限         |
|    限定于资源组     |    对资源组中的所有容器和 blob 具有读/写访问权限     |    对资源组中的所有容器和 blob 具有读取访问权限     |    对资源组中的所有队列具有读/写访问权限     |    对资源组中的所有队列具有读取访问权限     |
|    限定于存储帐户    |    对存储帐户中的所有容器和 blob 具有读/写访问权限    |    对存储帐户中的所有容器和 blob 具有读取访问权限    |    对存储帐户中的所有队列具有读/写访问权限    |    对存储帐户中的所有队列具有读取访问权限    |
|    限定于容器/队列    |    对指定容器及其 blob 具有读/写访问权限              |    对指定容器及其 blob 具有读取访问权限              |    对指定队列具有读/写访问权限                  |    对指定队列具有读取访问权限                    |

> [!NOTE]
> 作为 Azure 存储帐户的所有者，系统不会自动向你分配数据访问权限。 你必须为自己显式分配一个用于 Azure 存储的 RBAC 角色。 可以在订阅、资源组、存储帐户、容器或队列级别分配它。

有关调用 Azure 存储操作所需的权限的详细信息，请参阅[用于调用 REST 操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。

以下部分介绍如何分配限定于存储帐户或限定于单个容器的角色。

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>在 Azure 门户中分配限定于存储帐户的角色

若要在 Azure 门户中分配内置角色，以授予对存储帐户中所有容器或队列的访问权限，请执行以下操作：

1. 在 [Azure 门户](https://azure.portal.com/)中导航到存储帐户。
2. 选择存储帐户，然后选择“访问控制(IAM)”以显示该帐户的访问控制设置。 单击“添加”按钮以添加新角色。

    ![显示存储访问控制设置的屏幕截图](media/storage-auth-aad-rbac/portal-access-control.png)

3. 在“添加权限”窗口中，选择要分配给 Azure AD 标识的角色。 然后搜索以找到要为其分配该角色的标识。 例如，下图显示分配给用户的“存储 Blob 数据读取器(预览)”角色。

    ![显示如何分配 RBAC 角色的屏幕截图](media/storage-auth-aad-rbac/add-rbac-role.png)

4. 单击“ **保存**”。 分配有该角色的标识列出在该角色下。 例如，下图显示添加的用户现在对存储帐户中的所有 blob 数据具有读取权限。

    ![显示分配有角色的用户列表的屏幕截图](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>在 Azure 门户中分配限定于容器或队列的角色

分配限定于容器或队列的内置角色的步骤类似。 此处所示的过程将分配限定于容器的角色，但你可以按照相同的步骤分配限定于队列的角色： 

1. 在 [Azure 门户](https://azure.portal.com/)中，导航到存储帐户并显示该帐户的“概述”。
2. 在“Blob 服务”下选择“浏览 Blob”。 
3. 找到要针对其分配角色的容器，并显示该容器的设置。 
4. 选择“访问控制(IAM)”以显示容器的访问控制设置。
5. 在“添加权限”窗口中，选择要分配给 Azure AD 标识的角色。 然后搜索以找到要为其分配该角色的标识。
6. 单击“ **保存**”。 分配有该角色的标识列出在该角色下。 例如，下图显示添加的用户现在对名为 *sample-container* 的容器中的数据具有读取权限。

    ![显示分配有角色的用户列表的屏幕截图](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>后续步骤

- 若要详细了解 RBAC，请参阅[基于角色的访问控制入门](../../role-based-access-control/overview.md)。
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 RBAC 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-powershell.md)
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-rest.md)
- 若要了解如何从存储应用程序内授权访问容器和队列，请参阅[将 Azure AD 与 Azure 存储应用程序配合使用](storage-auth-aad-app.md)。
- 有关适用于 Azure 容器和队列的 Azure AD 集成的其他信息，请参阅 Azure 存储团队博客文章[宣布推出适用于 Azure 存储的 Azure AD 身份验证预览版](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。
- 