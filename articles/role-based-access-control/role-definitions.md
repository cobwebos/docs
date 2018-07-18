---
title: 了解 Azure RBAC 中的角色定义 | Microsoft Docs
description: 了解基于角色的访问控制 (RBAC) 中的角色定义，以便对 Azure 中的资源进行精细的访问权限管理。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 1d594b91b85a1bad3bbaa69bc27e62a4829a5661
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37438265"
---
# <a name="understand-role-definitions"></a>了解角色定义

如果想要了解角色是如何工作的，或者要创建自己的[自定义角色](custom-roles.md)，那么了解角色的定义方法会很有帮助。 本文介绍角色定义的详细信息，并提供了一些示例。

## <a name="role-definition-structure"></a>角色定义结构

*角色定义*是权限的集合。 它有时简称为“角色”。 角色定义列出了可以执行的操作，例如读取、写入和删除。 它还可以列出不能执行的操作，或者与基础数据相关的操作。 角色定义具有以下结构：

```
assignableScopes []
description
id
name
permissions []
  actions []
  dataActions []
  notActions []
  notDataActions []
roleName
roleType
type
```

使用以下格式的字符串指定操作：

- `{Company}.{ProviderName}/{resourceType}/{action}`

操作字符串的 `{action}` 部分指定可以对某个资源类型执行的操作类型。 例如，将在 `{action}` 中看到以下子字符串：

| 操作子字符串    | 说明         |
| ------------------- | ------------------- |
| `*` | 通配符授予对与字符串匹配的所有操作的访问权限。 |
| `read` | 允许读取操作 (GET)。 |
| `write` | 允许写入操作（PUT、POST 和 PATCH）。 |
| `delete` | 允许删除操作 (DELETE)。 |

下面是 JSON 格式的[参与者](built-in-roles.md#contributor)角色定义。 `actions` 下的通配符 (`*`) 操作表示分配给此角色的主体可以执行所有操作，换句话说，它可以管理所有内容。 这包括将来定义的操作，因为 Azure 会添加新的资源类型。 `notActions` 下的操作会从 `actions` 中减去。 就[参与者](built-in-roles.md#contributor)角色而言，`notActions` 去除了此角色管理资源访问权限以及分配资源访问权限的能力。

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-and-data-operations-preview"></a>管理和数据操作（预览版）

管理操作的基于角色的访问控制在角色定义的 `actions` 和 `notActions` 属性中指定。 下面是 Azure 中管理操作的一些示例：

- 管理存储帐户的访问权限
- 创建、更新或删除 blob 容器
- 删除资源组及其所有资源

数据不会继承管理访问权限。 此分隔可防止带通配符 (`*`) 的角色无限制地访问数据。 例如，如果用户对订阅具有[读取者](built-in-roles.md#reader)角色，则他们可以查看存储帐户，但他们默认无法查看基础数据。

以前，基于角色的访问控制不用于数据操作。 数据操作的授权根据资源提供程序的不同而异。 用于管理操作的同一基于角色的访问控制授权模型已扩展到数据操作（当前为预览版）。

为了支持数据操作，已将新的数据属性添加到角色定义结构。 数据操作在 `dataActions` 和 `notDataActions` 属性中指定。 通过添加这些数据属性，可在管理与数据之间保持隔离。 这可以防止包含通配符 (`*`) 的当前角色分配突然访问数据。 下面是可在 `dataActions` 和 `notDataActions` 中指定的一些数据操作：

- 读取容器中的 Blob 列表
- 在容器中写入存储 Blob
- 删除队列中的消息

下面是[存储 Blob 数据读者（预览版）](built-in-roles.md#storage-blob-data-reader-preview)角色定义，其中包含 `actions` 和 `dataActions` 属性中的操作。 使用此角色可以读取 Blob 容器以及基础 Blob 数据。

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Allows for read access to Azure Storage blob containers and data.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/read"
        ],
        "additionalProperties": {},
        "dataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
        ],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Storage Blob Data Reader (Preview)",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

只能将数据操作添加到 `dataActions` 和 `notDataActions` 属性。 资源提供程序通过将 `isDataAction` 属性设置为 `true`，来识别哪些操作是数据操作。 若要查看 `isDataAction` 为 `true` 的操作列表，请参阅[资源提供程序操作](resource-provider-operations.md)。 没有数据操作的角色不需要在角色定义中包含 `dataActions` 和 `notDataActions` 属性。

所有管理操作 API 调用的授权由 Azure 资源管理器处理。 数据操作 API 调用的授权由资源提供程序或 Azure 资源管理器处理。

### <a name="data-operations-example"></a>数据操作示例

为了更好地了解管理和数据操作的工作原理，让我们考虑一个具体的示例。 在订阅范围为 Alice 分配了[所有者](built-in-roles.md#owner)角色。 在存储帐户范围为 Bob 分配了[存储 Blob 数据参与者（预览版）](built-in-roles.md#storage-blob-data-contributor-preview)角色。 下图演示了此示例。

![基于角色的访问控制已得到扩展，支持管理和数据操作](./media/role-definitions/rbac-management-data.png)

Alice 的[所有者](built-in-roles.md#owner)角色和 Bob 的[存储 Blob 数据参与者（预览版）](built-in-roles.md#storage-blob-data-contributor-preview)角色具有以下操作：

所有者

&nbsp;&nbsp;&nbsp;&nbsp;操作<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

存储 Blob 数据参与者（预览版）

&nbsp;&nbsp;&nbsp;&nbsp;操作<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

由于 Alice 具有订阅范围的通配符 (`*`) 操作，她的权限将向下继承，使她可以执行所有管理操作。 但是，Alice 无法执行数据操作。 例如，默认情况下，Alice 无法读取容器中的 Blob，但可以读取、写入和删除容器。

Bob 的权限限制为[存储 Blob 数据参与者（预览版）](built-in-roles.md#storage-blob-data-contributor-preview)角色中指定的 `actions` 和 `dataActions`。 Bob 可以基于角色执行管理和数据操作。 例如，Bob 可以读取、写入和删除指定存储帐户中的容器，并可以读取、写入和删除 Blob。

### <a name="what-tools-support-using-rbac-for-data-operations"></a>哪些工具支持使用 RBAC 进行数据操作？

若要查看和处理数据操作，必须安装正确版本的工具或 SDK：

| 工具  | 版本  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-azurerm-ps) | 5.6.0 或更高版本 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 或更高版本 |
| [Azure for .NET](/dotnet/azure/) | 2.8.0-preview 或更高版本 |
| [Azure SDK for Go](/go/azure/azure-sdk-go-install) | 15.0.0 或更高版本 |
| [Azure for Java](/java/azure/) | 1.9.0 或更高版本 |
| [Azure for Python](/python/azure) | 0.40.0 或更高版本 |
| [用于 Ruby 的 Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 或更高版本 |

## <a name="actions"></a>actions

`actions` 权限指定该角色允许执行的管理操作。 它是操作字符串的集合，可标识 Azure 资源提供程序的安全对象操作。 下面是一些可以在 `actions` 中使用的管理操作的示例。

| 操作字符串    | 说明         |
| ------------------- | ------------------- |
| `*/read` | 向所有 Azure 资源提供程序的所有资源类型的读取操作授予访问权限。|
| `Microsoft.Compute/*` | 向 Microsoft.Compute 资源提供程序中的所有资源类型的所有操作授予访问权限。|
| `Microsoft.Network/*/read` | 向 Microsoft.Network 资源提供程序中的所有资源类型的读取操作授予访问权限。|
| `Microsoft.Compute/virtualMachines/*` | 向虚拟机及其子资源类型的所有操作授予访问权限。|
| `microsoft.web/sites/restart/Action` | 授予重启 Web 应用的访问权限。|

## <a name="notactions"></a>notActions

`notActions` 权限指定从允许的 `actions` 中排除的管理操作。 如果排除受限制的操作可以更方便地定义希望允许的操作集，则使用 `notActions` 权限。 通过从 `actions` 操作中减去 `notActions` 操作可以计算出角色授予的访问权限（有效权限）。

> [!NOTE]
> 如果用户分配到的一个角色排除了 `notActions` 中的一个操作，而分配到的第二个角色向同一操作授予访问权限，则用户可以执行该操作。 `notActions` 不是拒绝规则 - 它只是一个简便方法，可在需要排除特定操作时创建一组允许的操作。
>

## <a name="dataactions-preview"></a>dataActions（预览版）

`dataActions` 权限指定此角色允许对该对象中的数据执行的数据操作。 例如，如果某个用户对某个存储帐户拥有读取 Blob 数据的访问权限，则该用户可以读取该存储帐户中的 Blob。 下面是可在 `dataActions` 中使用的一些数据操作的示例。

| 操作字符串    | 说明         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | 返回 Blob 或 Blob 列表。 |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | 返回写入 Blob 的结果。 |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | 返回消息。 |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | 返回消息，或返回写入或删除消息的结果。 |

## <a name="notdataactions-preview"></a>notDataActions（预览版）

`notDataActions` 权限指定从允许的 `dataActions` 中排除的数据操作。 通过从 `dataActions` 操作中减去 `notDataActions` 操作可以计算出角色授予的访问权限（有效权限）。 每个资源提供程序提供相应的一组 API 用于实现数据操作。

> [!NOTE]
> 如果用户分配到的一个角色排除了 `notDataActions` 中的某个数据操作，而分配到的第二个角色向同一数据操作授予访问权限，则该用户可以执行该数据操作。 `notDataActions` 不是拒绝规则 - 它只是一个简便方法，可在需要排除特定数据操作时创建一组允许的数据操作。
>

## <a name="assignablescopes"></a>assignableScopes

`assignableScopes` 属性指定角色可供分配的范围（管理组（目前处于预览状态）、订阅、资源组或资源）。 可以让角色只在需要它的订阅或资源组中进行分配，而不影响其他订阅或资源组的用户体验。 必须使用至少一个管理组、订阅、资源组或资源 ID。

内置角色已将 `assignableScopes` 设置为根范围 (`"/"`)。 根范围指示角色可供在所有范围中进行分配。 有效的可分配范围的示例包括：

| 场景 | 示例 |
|----------|---------|
| 角色可供在单个订阅中进行分配 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| 角色可供在两个订阅中进行分配 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| 角色只能在网络资源组中进行分配 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| 角色可供在所有范围中进行分配 | `"/"` |

有关自定义角色的 `assignableScopes` 的信息，请参阅[自定义角色](custom-roles.md)。

## <a name="next-steps"></a>后续步骤

* [内置角色](built-in-roles.md)
* [自定义角色](custom-roles.md)
* [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)
