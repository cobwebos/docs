---
title: 配置对象复制（预览）
titleSuffix: Azure Storage
description: 了解如何配置对象复制，以将块 blob 从一个存储帐户中的容器异步复制到另一个存储帐户中的容器。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd85504ac2321310288efe5d0a1ef7dfcde60f21
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566057"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>为块 blob 配置对象复制（预览）

对象复制（预览）在源存储帐户和目标帐户之间异步复制块 blob。 若要详细了解对象复制，请参阅[对象复制（预览）](object-replication-overview.md)。

配置对象复制时，需要创建复制策略，以指定源存储帐户和目标帐户。 复制策略包括一个或多个规则，用于指定源容器和目标容器，并指明复制源容器中的哪些块 blob。

本文介绍了如何使用 Azure 门户、PowerShell 或 Azure CLI 为存储帐户配置对象复制。 还可以使用 Azure 存储资源提供程序客户端库之一来配置对象复制。

## <a name="create-a-replication-policy-and-rules"></a>创建复制策略和规则

在配置对象复制前，如果还没有源存储帐户和目标存储帐户，请先创建它们。 这两个帐户都必须是常规用途 v2 存储帐户。 有关详细信息，请参阅[创建 Azure 存储帐户](../common/storage-account-create.md)。

存储帐户可用作最多两个目标帐户的源帐户。 并且目标帐户不能有两个以上的源帐户。 源帐户和目标帐户可能全都位于不同的区域。 可以配置单独的复制策略，以便将数据复制到每个目标帐户。

在开始之前，请确保已注册以下功能预览：

- [对象复制（预览）](object-replication-overview.md)
- [Blob 版本控制（预览版）](versioning-overview.md)
- [Azure Blob 存储中的更改源支持（预览）](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

在 Azure 门户中配置对象复制前，如果还没有源容器和目标容器，请在它们各自的存储帐户中创建它们。 此外，还要在源帐户上启用 blob 版本控制和更改源，并在目标帐户上启用 blob 版本控制。

若要在 Azure 门户中创建复制策略，请按照以下步骤操作：

1. 在 Azure 门户中，转到源存储帐户。
1. 在 **Blob 服务**下，选择 **对象复制**。
1. 选择 " **设置复制规则**"。
1. 选择目标订阅和存储帐户。
1. 在“容器对”部分中，选择源帐户中的源容器，以及目标帐户中的目标容器。 每个复制策略最多可以创建 10 个容器对。

    下图展示了一组复制规则。

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="展示了 Azure 门户中复制规则的屏幕截图":::

1. 如果需要，指定一个或多个筛选器，以便只复制与前缀模式匹配的 blob。 例如，如果指定前缀 `b`，则只会复制名称以此字母开头的 blob。 可以将虚拟目录指定为前缀的一部分。 前缀字符串不支持通配符。

    下图展示了限制在复制规则中复制哪些 blob 的筛选器。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="展示了复制规则的筛选器的屏幕截图":::

1. 默认情况下，复制范围设置为只复制新对象。 若要复制容器中的所有对象，或从自定义日期和时间开始复制对象，请选择“更改”链接，然后为容器对配置复制范围。

    下图展示了自定义复制范围。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="展示了对象复制的自定义复制范围的屏幕截图":::

1. 选择“保存并应用”，以创建复制策略，并开始复制数据。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建复制策略，请先安装 [2.0.1 版-预览版](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) 或更高版本。 若要安装预览版模块，请按照以下步骤操作：

1. 使用“设置”下的“应用和功能”设置从 Windows 中卸载以前安装的所有 Azure PowerShell。

1. 确保已安装 PowerShellGet 最新版本。 打开 Windows PowerShell 窗口，然后运行以下命令以安装最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    安装 PowerShellGet 后关闭并重新打开 PowerShell 窗口。

1. 安装最新版本的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 安装 Az.Storage 预览版模块：

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

有关如何安装 Azure PowerShell 的详细信息，请参阅[使用 PowerShellGet 安装 Azure PowerShell](/powershell/azure/install-az-ps)。

下面的示例展示了如何在源帐户和目标帐户上创建复制策略。 请注意将尖括号中的值替换为你自己的值：

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建复制策略，请先安装 Azure 存储的预览扩展：

```azurecli
az extension add -n storage-or-preview
```

接下来，使用 Azure 凭据登录：

```azurecli
az login
```

在源存储帐户和目标存储帐户上启用 blob 版本控制，在源帐户上启用更改源。 请注意将尖括号中的值替换为你自己的值：

```azurecli
az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

在它们各自的存储帐户中创建源容器和目标容器。

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container4 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name source-container4 \
    --auth-mode login
```

在目标帐户上新建复制策略及其关联的规则。

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

使用策略 ID 在源帐户上创建策略。

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>删除复制策略

若要删除复制策略及其关联的规则，请使用 Azure 门户、PowerShell 或 CLI。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中删除复制策略，请按照以下步骤操作：

1. 在 Azure 门户中，转到源存储帐户。
1. 在“设置”下，选择“对象复制”。
1. 单击策略名称旁边的“更多”按钮。
1. 选择“删除规则”。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要删除复制策略，请从源帐户和目标帐户中都删除复制策略。 删除策略还会删除与之关联的任何规则。

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要删除复制策略，请从源帐户和目标帐户中都删除复制策略。 删除策略还会删除与之关联的任何规则。

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>后续步骤

- [对象复制概述（预览）](object-replication-overview.md)
