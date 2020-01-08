---
title: 使用 PowerShell 为数据访问分配 RBAC 角色
titleSuffix: Azure Storage
description: 了解如何使用 PowerShell 通过基于角色的访问控制（RBAC）向 Azure Active Directory 安全主体分配权限。 Azure 存储通过 Azure AD 支持用于身份验证的内置和自定义 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1413035c879198cf333aeeb5d8fe993162939172
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460582"
---
# <a name="use-powershell-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>使用 PowerShell 分配用于访问 blob 和队列数据的 RBAC 角色

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储定义了一组内置的 RBAC 角色，它们包含用于访问容器或队列的通用权限集。

将 RBAC 角色分配到 Azure AD 安全主体时，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体或[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介绍如何使用 Azure PowerShell 列出内置 RBAC 角色并将其分配给用户。 有关使用 Azure PowerShell 的详细信息，请参阅[Azure PowerShell 概述](https://docs.microsoft.com/powershell/azure/overview)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 和队列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>确定资源范围

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>列出可用的 RBAC 角色

若要列出 Azure PowerShell 的可用内置 RBAC 角色，请使用[AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)命令：

```powershell
Get-AzRoleDefinition | FT Name, Description
```

你将看到列出的内置 Azure 存储数据角色，以及 Azure 的其他内置角色：

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>向安全主体分配 RBAC 角色

若要将 RBAC 角色分配到安全主体，请使用[AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)命令。 根据分配的范围，命令的格式可能有所不同。 若要运行此命令，需要在相应的范围内分配 "所有者" 或 "参与者" 角色。 下面的示例演示如何将角色分配给不同范围内的用户，但你可以使用相同的命令为任何安全主体分配角色。

### <a name="container-scope"></a>容器范围

若要将作用域分配到容器，请指定包含 `--scope` 参数的容器范围的字符串。 容器的作用域格式为：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

下面的示例将**存储 Blob 数据参与者**角色分配给一个用户，其作用域为容器*为的容器。* 请确保将示例值和占位符值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>队列范围

若要将作用域分配给队列，请为 `--scope` 参数指定包含队列范围的字符串。 队列的作用域格式为：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

下面的示例将**存储队列数据参与者**角色分配给一个用户，其作用域为 "*示例-队列*" 队列。 请确保将示例值和占位符值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>存储帐户范围

若要将作用域分配给存储帐户，请为 `--scope` 参数指定存储帐户资源的作用域。 存储帐户的作用域格式为：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

下面的示例演示如何将**存储 Blob 数据读取器**角色的作用域限定为存储帐户级别的用户。 请确保将示例值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>资源组作用域

若要将作用域分配给资源组，请为 `--resource-group` 参数指定资源组名称或 ID。 下面的示例将**存储队列数据读取器**角色分配给资源组级别的用户。 请确保用自己的值替换括号中的示例值和占位符值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>订阅范围

若要将作用域分配到订阅，请为 `--scope` 参数指定订阅的作用域。 订阅的作用域格式为：

```
/subscriptions/<subscription>
```

下面的示例演示如何将**存储 Blob 数据读取器**角色分配给存储帐户级别的用户。 请确保将示例值替换为你自己的值： 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>后续步骤

- [使用 RBAC 和 Azure PowerShell 管理对 Azure 资源的访问权限](../../role-based-access-control/role-assignments-powershell.md)
- [通过 Azure CLI 使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-cli.md)
- [在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)
