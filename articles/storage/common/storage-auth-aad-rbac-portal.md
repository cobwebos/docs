---
title: Use the Azure portal to manage Azure AD access rights to blob and queue data with RBAC - Azure Storage | Microsoft Docs
description: Use role-based access control (RBAC) from the Azure portal to assign access to containers and queues to security principals. Azure Storage supports built-in and custom RBAC roles for authentication via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b11b2c42087b8724c7d90b87bc33965eb7270dc6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422001"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Grant access to Azure blob and queue data with RBAC in the Azure portal

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure Storage defines a set of built-in RBAC roles that encompass common sets of permissions used to access blob or queue data. 

When an RBAC role is assigned to an Azure AD security principal, Azure grants access to those resources for that security principal. 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 An Azure AD security principal may be a user, a group, an application service principal, or a [managed identity for Azure resources](../../active-directory/managed-identities-azure-resources/overview.md).

This article describes how to use the Azure portal to assign RBAC roles. The Azure portal provides a simple interface for assigning RBAC roles and managing access to your storage resources. You can also assign RBAC roles for blob and queue resources using Azure command-line tools or the Azure Storage management APIs. For more information about RBAC roles for storage resources, see [Authenticate access to Azure blobs and queues using Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 和队列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determine resource scope 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Assign RBAC roles using the Azure portal

After you have determined the appropriate scope for a role assignment, navigate to that resource in the Azure portal. Display the **Access Control (IAM)** settings for the resource, and follow these instructions to manage role assignments:

1. Assign the appropriate Azure Storage RBAC role to grant access to an Azure AD security principal.

1. Assign the Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) role to users who need to access containers or queues via the Azure portal using their Azure AD credentials. 

The following sections describe each of these steps in more detail.

> [!NOTE]
> 作为 Azure 存储帐户的所有者，系统不会自动向你分配数据访问权限。 你必须为自己显式分配一个用于 Azure 存储的 RBAC 角色。 可以在订阅、资源组、存储帐户、容器或队列级别分配它。
> 
> You cannot assign a role scoped to a container or queue if your storage account has a hierarchical namespace enabled.

### <a name="assign-a-built-in-rbac-role"></a>Assign a built-in RBAC role

Before you assign a role to a security principal, be sure to consider the scope of the permissions you are granting. Review the [Determine resource scope](#determine-resource-scope) section to decide the appropriate scope.

此处所示的过程将分配限定于容器的角色，但你可以按照相同的步骤分配限定于队列的角色： 

1. In the [Azure portal](https://portal.azure.com), go to your storage account and display the **Overview** for the account.
1. 在“服务”下选择“Blob”。 
1. 找到要针对其分配角色的容器，并显示该容器的设置。 
1. 选择“访问控制(IAM)”以显示容器的访问控制设置。 选择“角色分配”选项卡以查看角色分配列表。

    ![Screenshot showing container access control settings](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. 单击“添加角色分配”按钮以添加一个新角色。
1. In the **Add role assignment** window, select the Azure Storage role that you want to assign. Then search to locate the security principal to which you want to assign that role.

    ![显示如何分配 RBAC 角色的屏幕截图](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. 单击“保存”。 分配有该角色的标识列出在该角色下。 例如，下图显示添加的用户现在对名为 *sample-container* 的容器中的数据具有读取权限。

    ![Screenshot showing list of users assigned to a role](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

You can follow similar steps to assign a role scoped to the storage account, resource group, or subscription.

### <a name="assign-the-reader-role-for-portal-access"></a>Assign the Reader role for portal access

When you assign a built-in or custom role for Azure Storage to a security principal, you are granting permissions to that security principal to perform operations on data in your storage account. The built-in **Data Reader** roles provide read permissions for the data in a container or queue, while the built-in **Data Contributor** roles provide read, write, and delete permissions to a container or queue. Permissions are scoped to the specified resource.  

For example, if you assign the **Storage Blob Data Contributor** role to user Mary at the level of a container named **sample-container**, then Mary is granted read, write, and delete access to all of the blobs in that container.

However, if Mary wants to view a blob in the Azure portal, then the **Storage Blob Data Contributor** role by itself will not provide sufficient permissions to navigate through the portal to the blob in order to view it. Additional Azure AD permissions are required to navigate through the portal and view the other resources that are visible there.

If your users need to be able to access blobs in the Azure portal, then assign them an additional RBAC role, the [Reader](../../role-based-access-control/built-in-roles.md#reader) role, to those users, at the level of the storage account or above. The **Reader** role is an Azure Resource Manager role that permits users to view storage account resources, but not modify them. It does not provide read permissions to data in Azure Storage, but only to account management resources.

Follow these steps to assign the **Reader** role so that a user can access blobs from the Azure portal. In this example, the assignment is scoped to the storage account:

1. 在 [Azure 门户](https://portal.azure.com)中导航到存储帐户。
1. Select **Access control (IAM)** to display the access control settings for the storage account. 选择“角色分配”选项卡以查看角色分配列表。
1. In the **Add role assignment** window, select the **Reader** role. 
1. From the **Assign access to** field, select **Azure AD user, group, or service principal**.
1. Search to locate the security principal to which you want to assign the role.
1. Save the role assignment.

> [!NOTE]
> Assigning the Reader role is necessary only for users who need to access blobs or queues using the Azure portal. 

## <a name="next-steps"></a>后续步骤

- For more information about RBAC roles for storage resources, see [Authenticate access to Azure blobs and queues using Azure Active Directory](storage-auth-aad.md). 
- 若要详细了解 RBAC，请参阅[什么是基于角色的访问控制 (RBAC)？](../../role-based-access-control/overview.md)
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 RBAC 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-powershell.md)
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-rest.md)
- 若要了解如何从存储应用程序内授权访问容器和队列，请参阅[将 Azure AD 与 Azure 存储应用程序配合使用](storage-auth-aad-app.md)。
