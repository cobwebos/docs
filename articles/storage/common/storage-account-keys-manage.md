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
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069653"
---
# <a name="manage-storage-account-access-keys"></a>管理存储帐户访问密钥

当你创建存储帐户时，Azure 会生成两个 512 位存储帐户访问密钥。 这些密钥可用于通过共享密钥授权来授予对你存储帐户中数据的访问权限。

Microsoft 建议使用 Azure Key Vault 来管理访问密钥，并且定期轮换和重新生成密钥。 使用 Azure 密钥保管库可以轻松轮换密钥，而无需中断应用程序。 还可以手动轮换密钥。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>查看帐户访问密钥

可以使用 Azure 门户、PowerShell 或 Azure CLI 查看和复制帐户访问密钥。 Azure 门户还为你的存储帐户提供了一个可供复制的连接字符串。

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要从 Azure 门户查看和复制存储帐户访问密钥或连接字符串，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中导航到存储帐户。
1. 在“设置”下，选择“访问密钥” 。 此时会显示帐户访问密钥，以及每个密钥的完整连接字符串。
1. 找到“key1”下面的“密钥”值，单击“复制”按钮复制该帐户密钥。  
1. 或者，可以复制整个连接字符串。 找到“密钥 1”下面的“连接字符串”值，单击“复制”按钮复制该连接字符串。  

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="显示如何在 Azure 门户中查看访问密钥的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 检索帐户访问密钥，请调用 [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) 命令。

以下示例将检索第一个密钥。 若要检索第二个密钥，请使用 `Value[1]` 而不是 `Value[0]`。 请记得将括号中的占位符值替换为你自己的值。

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 列出帐户访问密钥，请调用 [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) 命令，如以下示例中所示。 请记得将括号中的占位符值替换为你自己的值。 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

可以使用这两个密钥中的任何一个来访问 Azure 存储，但通常情况下，最好使用第一个密钥，并保留第二个密钥在轮换密钥时使用。

若要查看或读取帐户的访问密钥，用户必须是服务管理员，或者分配到包含“Microsoft.Storage/storageAccounts/listkeys/action”的 Azure 角色。 包含此操作的 Azure 内置角色有“所有者”、“贡献者”和“存储帐户密钥操作员服务角色”等  。 有关服务管理员角色的详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。 若要详细了解 Azure 存储的内置角色，请参阅 [Azure RBAC 的 Azure 内置角色](../../role-based-access-control/built-in-roles.md#storage)中的“存储”部分。

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>使用 Azure 密钥保管库管理访问密钥

Microsoft 建议使用 Azure Key Vault 来管理和旋转访问密钥。 应用程序可以安全地访问密钥保管库中的密钥，这样就可以避免使用应用程序代码来存储密钥。 有关使用密钥保管库进行密钥管理的详细信息，请参阅以下文章：

- [使用 Azure 密钥保管库和 PowerShell 管理存储帐户密钥](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [使用 Azure 密钥保管库和 Azure CLI 管理存储帐户密钥](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>手动轮换访问密钥

Microsoft 建议定期轮换访问密钥，以确保存储帐户安全。 如果可能，请使用 Azure 密钥保管库管理访问密钥。 如果不使用密钥保管库，将需要手动轮换密钥。

系统会分配两个访问密钥，以便可以轮换密钥。 拥有两个密钥可确保应用程序在整个轮换过程中能够持续访问 Azure 存储。

> [!WARNING]
> 重新生成访问密钥可能会影响依赖于存储帐户密钥的所有应用程序或 Azure 服务。 使用帐户密钥访问存储帐户的任何客户端必须更新为使用新密钥，其中包括媒体服务、云、桌面和移动应用程序，以及适用于 Azure 存储的图形用户界面应用程序，例如 [Azure存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中轮换存储帐户访问密钥：

1. 更新应用程序代码中的连接字符串以引用存储帐户的辅助访问密钥。
1. 在 [Azure 门户](https://portal.azure.com)中导航到存储帐户。
1. 在“设置”下，选择“访问密钥” 。
1. 若要为存储帐户重新生成主访问密钥，请选择主访问密钥旁边的“重新生成”按钮。
1. 更新代码中的连接字符串以引用新的主访问密钥。
1. 以相同方式重新生成辅助访问密钥。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 轮换存储帐户访问密钥：

1. 更新应用程序代码中的连接字符串以引用存储帐户的辅助访问密钥。
1. 调用 [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) 命令以重新生成主访问密钥，如以下示例中所示：

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. 更新代码中的连接字符串以引用新的主访问密钥。
1. 以相同方式重新生成辅助访问密钥。 若要重新生成辅助密钥，请将 `key2`（而不是 `key1`）用作密钥名称。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 轮换存储帐户访问密钥：

1. 更新应用程序代码中的连接字符串以引用存储帐户的辅助访问密钥。
1. 调用 [az storage account keys renew](/cli/azure/storage/account/keys#az-storage-account-keys-renew) 命令以重新生成主访问密钥，如以下示例中所示：

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. 更新代码中的连接字符串以引用新的主访问密钥。
1. 以相同方式重新生成辅助访问密钥。 若要重新生成辅助密钥，请将 `key2`（而不是 `key1`）用作密钥名称。

---

> [!NOTE]
> Microsoft 建议同一时间在所有应用程序中只使用一个密钥。 如果在某些地方使用密钥 1 并在其他地方使用密钥 2，则无法在没有部分应用程序失去访问的情况下轮转密钥。

若要轮换帐户的访问密钥，用户必须是服务管理员，或者分配到包含“Microsoft.Storage/storageAccounts/regeneratekey/action”的 Azure 角色。 包含此操作的 Azure 内置角色有“所有者”、“贡献者”和“存储帐户密钥操作员服务角色”等  。 有关服务管理员角色的详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。 若要详细了解 Azure 存储的 Azure 内置角色，请参阅 [Azure RBAC 的 Azure 内置角色](../../role-based-access-control/built-in-roles.md#storage)中的“存储”部分。

## <a name="next-steps"></a>后续步骤

- [Azure 存储帐户概述](storage-account-overview.md)
- [创建存储帐户](storage-account-create.md)
