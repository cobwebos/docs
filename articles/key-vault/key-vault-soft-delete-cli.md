---
ms.assetid: ''
title: Azure Key Vault - 如何将软删除与 CLI 配合使用
description: 使用 CLI 代码剪辑进行软删除的用例示例
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: a9b80cae69c4e5852341385b98fcccc86d7959e9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927968"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>如何将 Key Vault 软删除与 CLI 配合使用

Azure Key Vault 的软删除功能可以恢复已删除的保管库和保管库对象。 软删除将具体探讨以下方案：

- 支持 Key Vault 的可恢复删除
- 支持密钥保管库对象、密钥、机密和证书的可恢复删除

## <a name="prerequisites"></a>先决条件

- Azure CLI 2.0 - 如果环境没有此设置，请参阅[使用 CLI 2.0 管理 Key Vault](key-vault-manage-with-cli2.md)。

有关 CLI Key Vault 的特定参考信息，请参阅 [Azure CLI 2.0 Key Vault 参考](https://docs.microsoft.com/cli/azure/keyvault)。

## <a name="required-permissions"></a>所需的权限

Key Vault 操作通过基于角色的访问控制 (RBAC) 权限单独管理，如下所示：

| Operation | 说明 | 用户权限 |
|:--|:--|:--|
|列出|列出已删除的密钥保管库。|Microsoft.KeyVault/deletedVaults/read|
|恢复|还原已删除的密钥保管库。|Microsoft.KeyVault/vaults/write|
|清除|永久删除已删除的密钥保管库及其所有内容。|Microsoft.KeyVault/locations/deletedVaults/purge/action|

有关权限和访问控制的详细信息，请参阅[保护密钥保管库](key-vault-secure-your-key-vault.md)。

## <a name="enabling-soft-delete"></a>启用软删除

若要能够恢复已删除的密钥保管库或存储在密钥保管库中的对象，必须首先为该密钥保管库启用软删除。

### <a name="existing-key-vault"></a>现有的密钥保管库

对于名为 ContosoVault 的现有密钥保管库，请按如下所示启用软删除。 

>[!NOTE]
>目前，需要使用 Azure 资源管理器资源操作直接将“enableSoftDelete”属性写入 Key Vault 资源。

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>新的密钥保管库

通过向创建命令添加软删除启用标志，在创建时启用对新密钥保管库的软删除。

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>验证软删除支持

若要验证密钥保管库是否启用了软删除，请运行“显示”命令，并查看“启用软删除?” 属性及其设置（true 或 false）。

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>删除由软删除保护的密钥保管库

删除（或移除）密钥保管库的命令保持不变，但其行为根据是否启用了软删除而改变。

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>如果为没有启用软删除的密钥保管库运行上一个命令，则将永久删除此密钥保管库及其所有内容，而没有任何恢复选项。

### <a name="how-soft-delete-protects-your-key-vaults"></a>软删除如何保护密钥保管库

已启用软删除：

- 删除一个密钥保管库时，将它从其资源组中删除，放置在一个仅与其创建位置相关联的保留命名空间中。 
- 无法访问删除的密钥保管库中的对象（例如密钥、机密和证书），并且在包含它们的密钥保管库处于已删除状态时依然如此。 
- 处于已删除状态的密钥保管库的 DNS 名称仍然保留，因此无法创建同名的新密钥保管库。  

使用以下命令，可查看与订阅关联且处于已删除状态的密钥保管库：

```azurecli
az keyvault list-deleted
```

输出中的“资源 ID”是指该保管库的原始资源 ID。 由于此密钥保管库现在处于已删除状态，因此该资源 ID 不存在任何资源。 ID 字段可用于在恢复或清除时识别资源。 “计划清除日期”字段指示如果不对此已删除的保管库执行任何操作，将永久删除（清除）该保管库的时间。 用于计算“计划清除日期”的默认保留期是 90 天。

## <a name="recovering-a-key-vault"></a>恢复密钥保管库

若要恢复密钥保管库，需要指定密钥保管库名称、资源组和位置。 请注意已删除的密钥保管库的位置和资源组，以便用于密钥保管库恢复过程。

```azurecli
az keyvault recover --location westus --name ContosoVault
```

恢复密钥保管库后，将得到具有密钥保管库原始资源 ID 的新资源。 如果密钥保管库所在的资源组已被删除，则必须先创建同名的新资源组，然后才能恢复密钥保管库。

## <a name="key-vault-objects-and-soft-delete"></a>密钥保管库对象和软删除

下面介绍对于密钥“ContosoFirstKey”，在启用了软删除的名为“ContosoVault”的密钥保管库中，如何删除该密钥。

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

为软删除启用密钥保管库后，删除的密钥仍然显示为已删除，除非明确列出或检索已删除的密钥。 对处于已删除状态的密钥的大多数操作将失败，列出、恢复或清除已删除的密钥除外。 

例如，若要请求列出密钥保管库中已删除的密钥，请使用以下命令：

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>转换状态 

在启用了软删除的密钥保管库中删除密钥时，可能需要几秒钟时间完成转换。 在此转换状态期间，密钥可能不处于活动状态或已删除状态。 此命令将列出名为“ContosoVault”的密钥保管库中的所有已删除的密钥。

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>将软删除用于密钥保管库对象

就像密钥保管库一样，除非恢复或清除已删除的密钥、机密或证书，否则它将保持已删除状态最多 90 天。 

#### <a name="keys"></a>密钥

恢复已删除的密钥：

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

永久删除密钥：

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>清除密钥将永久删除，这意味着无法恢复。

“恢复”和“清除”操作在密钥保管库访问策略中各自具有相关联的权限。 用户或服务主体若要执行“恢复”或“清除”操作，必须在密钥保管库访问策略中具有该对象（密钥或机密）的相应权限。 默认情况下，使用“全部”快捷方式向用户授予所有权限时，“清除”权限不会添加到密钥保管库访问策略中。 必须明确授予“清除”权限。 例如，以下命令授予 user@contoso.com 对“ContosoVault”中的密钥执行多项操作（包括“清除”）的权限。

#### <a name="set-a-key-vault-access-policy"></a>设置密钥保管库访问策略

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> 如果现有密钥保管库刚刚启用软删除，则可能没有“恢复”和“清除”权限。

#### <a name="secrets"></a>机密

像密钥一样，密钥保管库中的机密都用自己的命令进行操作。 以下是删除、列出、恢复和清除机密的命令。

- 删除名为 SQLPassword 的机密： 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- 列出密钥保管库中所有已删除的机密： 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- 恢复处于已删除状态的机密： 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- 清除处于已删除状态的机密： 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>清除机密将永久删除，这意味着无法恢复。

## <a name="purging-and-key-vaults"></a>清除和密钥保管库

### <a name="key-vault-objects"></a>密钥保管库对象

清除密钥、机密或证书将永久删除，这意味着无法恢复。 然而，包含已删除对象的密钥保管库将保持不变，密钥保管库中的所有其他对象也将保持不变。 

### <a name="key-vaults-as-containers"></a>密钥保管库作为容器
清除密钥保管库时，将永久删除其所有内容（包括密钥、机密和证书）。 若要清除密钥保管库，请使用 `az keyvault purge` 命令。 可使用命令 `az keyvault list-deleted` 找到订阅中已删除的密钥保管库的位置。

```azurecli
az keyvault purge --location westus --name ContosoVault
```

>[!NOTE]
>清除密钥保管库将永久删除，这意味着无法恢复。

### <a name="purge-permissions-required"></a>所需的清除权限
- 若要清除已删除的密钥保管库，以便永久删除保管库及其所有内容，用户需要 RBAC 权限执行 Microsoft.KeyVault/locations/deletedVaults/purge/action 操作。 
- 若要列出已删除的密钥，用户需要 RBAC 权限执行 Microsoft.KeyVault/deletedVaults/read 权限。 
- 默认情况下，只有订阅管理员具有这些权限。 

### <a name="scheduled-purge"></a>计划清除

列出已删除的密钥保管库对象会显示 Key Vault 计划将其清除的时间。 “计划清除日期”字段指示如果不采取任何操作，将永久删除密钥保管库对象的时间。 默认情况下，已删除的密钥保管库对象的保留期为 90 天。

>[!NOTE]
>已清除的保管库对象（由“计划清除日期”字段触发清除操作）将被永久删除。 不可恢复。

## <a name="other-resources"></a>其他资源

- 有关 Key Vault 软删除功能的概述，请参阅 [Azure Key Vault 软删除概述](key-vault-ovw-soft-delete.md)。
- 有关 Azure Key Vault 使用情况的综述，请参阅 [Azure Key Vault 入门](key-vault-get-started.md)。

