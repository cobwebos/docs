---
title: 管理帐户访问密钥
titleSuffix: Azure Storage
description: 了解如何查看、管理和轮换存储帐户访问密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.openlocfilehash: 4ade2c2e60373298eecf4e85df7fffeae4f45207
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176612"
---
# <a name="manage-storage-account-access-keys"></a>管理存储帐户访问密钥

创建存储帐户时，Azure 会生成两个 512 位的存储帐户访问密钥。 这些密钥可用于通过共享密钥授权来授予对你存储帐户中数据的访问权限。

Microsoft 建议使用 Azure Key Vault 来管理访问密钥，并且定期轮换和重新生成密钥。 使用 Azure 密钥保管库可以轻松轮换密钥，而无需中断应用程序。 还可以手动轮换密钥。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>查看帐户访问密钥

你可以使用 Azure 门户、PowerShell 或 Azure CLI 查看和复制帐户访问密钥。 Azure 门户还为你的存储帐户提供了一个可供复制的连接字符串。

# <a name="portal"></a>[门户](#tab/azure-portal)

若要从 Azure 门户查看和复制存储帐户访问密钥或连接字符串，请执行以下操作：

1. 导航到 [Azure 门户](https://portal.azure.com)中的存储帐户。
1. 在 "**设置**" 下，选择 "**访问密钥**"。 此时会显示帐户访问密钥，以及每个密钥的完整连接字符串。
1. 找到 Key 下的**键值**，并单击 "**复制**" 按钮复制帐户密钥。 **key1**
1. 或者，可复制整个连接字符串。 找到“密钥 1”下面的“连接字符串”值，单击“复制”按钮复制该连接字符串。************

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="显示如何在 Azure 门户中查看访问密钥的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 检索帐户访问密钥，请调用[AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey)命令。

下面的示例检索第一个键。 若要检索第二个键`Value[1]` ，请`Value[0]`使用而不是。 请记得将括号中的占位符值替换为你自己的值。

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 列出帐户访问密钥，请调用[az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list)命令，如以下示例中所示。 请记得将括号中的占位符值替换为你自己的值。 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

你可以使用两个密钥中的任意一个来访问 Azure 存储空间，但一般而言，最好使用第一个密钥，并在轮换密钥时保留使用第二个密钥。

若要查看或读取帐户的访问密钥，用户必须是服务管理员，或者必须为其分配一个包含 storageAccounts **//listkeys/action**的 RBAC 角色。 包含此操作的某些内置 RBAC 角色是 "**所有者**"、"**参与者**" 和 "**存储帐户密钥操作员" 服务角色**角色。 有关服务管理员角色的详细信息，请参阅[经典订阅管理员角色、AZURE RBAC 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。 有关 Azure 存储的内置角色的详细信息，请参阅 azure [RBAC 的 azure 内置角色](../../role-based-access-control/built-in-roles.md#storage)中的**存储**部分。

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>使用 Azure 密钥保管库管理访问密钥

Microsoft 建议使用 Azure Key Vault 来管理和旋转访问密钥。 应用程序可以安全地访问密钥保管库中的密钥，这样就可以避免使用应用程序代码来存储密钥。 有关使用密钥保管库进行密钥管理的详细信息，请参阅以下文章：

- [使用 Azure 密钥保管库和 PowerShell 管理存储帐户密钥](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [使用 Azure 密钥保管库和 Azure CLI 管理存储帐户密钥](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>手动轮换访问密钥

Microsoft 建议定期轮换访问密钥，以确保存储帐户安全。 如果可能，请使用 Azure 密钥保管库管理访问密钥。 如果不使用密钥保管库，将需要手动轮换密钥。

分配了两个访问密钥，以便轮换使用。 拥有两个密钥可确保应用程序在整个轮换过程中能够持续访问 Azure 存储。

> [!WARNING]
> 重新生成访问密钥会影响依赖于存储帐户密钥的所有应用程序或 Azure 服务。 必须将使用帐户密钥访问存储帐户的所有客户端更新为使用新密钥，包括媒体服务、云、桌面和移动应用程序以及用于 Azure 存储的图形用户界面应用程序（如 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）。

# <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中轮换存储帐户访问密钥：

1. 更新应用程序代码中的连接字符串，以引用存储帐户的辅助访问密钥。
1. 导航到 [Azure 门户](https://portal.azure.com)中的存储帐户。
1. 在 "**设置**" 下，选择 "**访问密钥**"。
1. 若要为存储帐户重新生成主访问密钥，请选择主访问密钥旁边的 "**重新生成**" 按钮。
1. 更新代码中的连接字符串以引用新的主访问密钥。
1. 以相同方式重新生成辅助访问密钥。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 轮换存储帐户访问密钥：

1. 更新应用程序代码中的连接字符串，以引用存储帐户的辅助访问密钥。
1. 调用[AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey)命令以重新生成主访问密钥，如以下示例中所示：

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. 更新代码中的连接字符串以引用新的主访问密钥。
1. 以相同方式重新生成辅助访问密钥。 若要重新生成辅助密钥， `key2`请使用作为密钥名称， `key1`而不是。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 轮换存储帐户访问密钥：

1. 更新应用程序代码中的连接字符串，以引用存储帐户的辅助访问密钥。
1. 调用[az storage account keys 续订](/cli/azure/storage/account/keys#az-storage-account-keys-renew)命令来重新生成主访问密钥，如以下示例中所示：

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. 更新代码中的连接字符串以引用新的主访问密钥。
1. 以相同方式重新生成辅助访问密钥。 若要重新生成辅助密钥， `key2`请使用作为密钥名称， `key1`而不是。

---

> [!NOTE]
> Microsoft 建议同一时间在所有应用程序中只使用一个密钥。 如果在某些地方使用密钥 1 并在其他地方使用密钥 2，将无法在没有部分应用程序失去访问的情况下轮转密钥。

若要轮换帐户的访问密钥，用户必须是服务管理员，或者必须为其分配一个包含 storageAccounts **//regeneratekey/action**的 RBAC 角色。 包含此操作的某些内置 RBAC 角色是 "**所有者**"、"**参与者**" 和 "**存储帐户密钥操作员" 服务角色**角色。 有关服务管理员角色的详细信息，请参阅[经典订阅管理员角色、AZURE RBAC 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。 有关 Azure 存储的内置 RBAC 角色的详细信息，请参阅 azure [RBAC 的 azure 内置角色](../../role-based-access-control/built-in-roles.md#storage)中的**存储**部分。

## <a name="next-steps"></a>后续步骤

- [Azure 存储帐户概述](storage-account-overview.md)
- [创建存储帐户](storage-account-create.md)
