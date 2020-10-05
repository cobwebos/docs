---
title: 使用 PowerShell 为数据访问分配 Azure 角色
titleSuffix: Azure Storage
description: 了解如何使用 Azure PowerShell 模块通过 azure RBAC)  (azure RBAC 访问控制向 Azure Active Directory 安全主体分配权限。 Azure 存储支持通过 Azure AD 使用内置和 Azure 自定义角色进行身份验证。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d750e1f287ff7dfc5259b704355e026011fa872a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715808"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>使用 PowerShell 为 blob 和队列数据访问分配 Azure 角色

Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储定义了一组 Azure 内置角色，它们包含用于访问容器或队列的通用权限集。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介绍如何使用 Azure PowerShell 列出 Azure 内置角色并将其分配给用户。 有关使用 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 概述](https://docs.microsoft.com/powershell/azure/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>Blob 和队列的 Azure 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>确定资源范围

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>列出可用的 Azure 角色

若要使用 Azure PowerShell 列出可用的 Azure 内置角色，请使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令：

```powershell
Get-AzRoleDefinition | FT Name, Description
```

会看到列出了内置的 Azure 存储数据角色以及 Azure 的其他内置角色：

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>向安全主体分配 Azure 角色

若要将 Azure 角色分配给安全主体，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 命令。 命令的格式因分配范围而异。 为了运行此命令，需要在相应的范围内分配“所有者”和“参与者”角色。 以下示例显示如何在各种范围内为用户分配角色，但可以使用相同的命令将角色分配给任何安全主体。

### <a name="container-scope"></a>容器范围

若要分配容器范围的角色，请为 `--scope` 参数指定一个包含容器范围的字符串。 容器的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

以下示例为用户分配**存储 Blob 数据参与者**角色，其范围为名为 *sample-container* 的容器。 请务必将括号中的示例值和占位符值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>队列范围

若要分配队列范围的角色，请为 `--scope` 参数指定一个包含队列范围的字符串。 队列的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

以下示例为用户分配**存储队列数据参与者**角色，其范围为名为 *sample-queue* 的队列。 请务必将括号中的示例值和占位符值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>存储帐户范围

若要分配存储帐户范围的角色，请为 `--scope` 参数指定存储帐户资源的范围。 存储帐户的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

以下示例演示如何将“存储 Blob 数据读取者”角色的范围限定为存储帐户级别的用户  。 请务必将示例值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>资源组范围

若要分配资源组范围的角色，请为 `--resource-group` 参数指定资源组名称或 ID。 以下示例在资源组级别向用户分配“存储队列数据读取者”角色  。 请务必将括号中的示例值和占位符值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>订阅范围

若要分配订阅范围的角色，请为 `--scope` 参数指定订阅的范围。 订阅的范围采用以下格式：

```
/subscriptions/<subscription>
```

以下示例演示如何在存储帐户级别向用户分配“存储 Blob 数据读取者”角色  。 请务必将示例值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure PowerShell 模块添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-powershell.md)
- [使用 Azure CLI 分配 Azure 角色以访问 blob 和队列数据](storage-auth-aad-rbac-cli.md)
- [使用 Azure 门户为 blob 和队列数据分配 Azure 角色](storage-auth-aad-rbac-portal.md)
