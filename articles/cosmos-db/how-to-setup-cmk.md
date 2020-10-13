---
title: 为 Azure Cosmos DB 帐户配置客户管理的密钥
description: 了解如何使用 Azure Key Vault 为 Azure Cosmos DB 帐户配置客户管理的密钥
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/05/2020
ms.author: thweiss
ms.openlocfilehash: 9fa899e0f0de3b263baad7e44ed24d32d735b001
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836502"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>使用 Azure Key Vault 为 Azure Cosmos 帐户配置客户管理的密钥

存储在 Azure Cosmos 帐户中的数据会自动使用由 Microsoft 管理的密钥（服务管理的密钥）进行无缝加密。 还可以选择使用你自己托管的密钥（客户托管密钥****）来添加另一个加密层。

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="围绕客户数据的加密层":::

必须将客户托管密钥存储在 [Azure Key Vault](../key-vault/general/overview.md) 中，并为每个启用了客户托管密钥的 Azure Cosmos 帐户提供一个密钥。 此密钥用于加密存储在该帐户中的所有数据。

> [!NOTE]
> 当前，客户托管密钥仅适用于新的 Azure Cosmos 帐户。 应在创建帐户期间配置这些密钥。

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> 为你的 Azure 订阅注册 Azure Cosmos DB 资源提供程序

1. 登录到 [Azure 门户](https://portal.azure.com/)，转到 Azure 订阅，并在 **“设置”** 选项卡下选择 **“资源提供程序”** ：

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="围绕客户数据的加密层":::

1. 搜索“Microsoft DocumentDB”**** 资源提供程序。 确认该资源提供程序是否标记为已注册。 如果不是，请选择该资源提供程序，然后选择“注册”：****

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="围绕客户数据的加密层":::

## <a name="configure-your-azure-key-vault-instance"></a>配置 Azure Key Vault 实例

将客户管理的密钥与 Azure Cosmos DB 结合使用时，需要在计划用于托管加密密钥的 Azure Key Vault 实例上设置两个属性： **“软删除”** 和 **“清除保护”** 。

如果创建新的 Azure Key Vault 实例，请在创建过程中启用以下属性：

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="围绕客户数据的加密层":::

如果使用的是现有 Azure Key Vault 实例，则可以通过查看 Azure 门户中的“属性”**** 部分来验证是否已启用这些属性。 如果未启用任一属性，请参阅以下文章中的“启用软删除”和“启用清除保护”部分：

- [如何在 PowerShell 中使用软删除](../key-vault/general/soft-delete-powershell.md)
- [如何在 Azure CLI 中使用软删除](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>将访问策略添加到 Azure Key Vault 实例

1. 在 Azure 门户中，转到你打算用来托管加密密钥的 Azure Key Vault 实例。 在左侧菜单中选择“访问策略”：****

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="围绕客户数据的加密层":::

1. 选择“+ 添加访问策略”****。

1. 在“密钥权限”下拉菜单中，选择“获取”、“解包密钥”和“包装密钥”权限：

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="围绕客户数据的加密层":::

1. 在“选择主体”下，选择“未选择任何项”。**** **** 然后，搜索“Azure Cosmos DB”主体并选中（为了更容易查找，还可以按主体 ID `a232010e-820c-4083-83bb-3ace5fc29d0b` 搜索，这适用于任何 Azure 区域，主体 ID 为 `57506a73-e302-42a9-b869-6f12d9ec29e9` 的 Azure 政府区域除外）。 最后，选择底部的“选择”****。 如果列表中没有“Azure Cosmos DB”**** 主体，可能需要根据本文的[注册资源提供程序](#register-resource-provider)部分所述，重新注册 Microsoft.DocumentDB**** 资源提供程序）。

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="围绕客户数据的加密层":::

1. 选择“添加”以添加新的访问策略****。

1. 在密钥保管库实例上选择“保存”，以保存所有更改。

## <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中生成密钥

1. 在 Azure 门户中，转到你打算用来托管加密密钥的 Azure Key Vault 实例。 然后，从左侧菜单中选择“密钥”****：

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="围绕客户数据的加密层":::

1. 选择“生成/导入”****，为新密钥提供名称，并选择一个 RSA 密钥大小。 建议至少使用 3072，以获得最佳安全性。 然后选择“创建”****：

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="围绕客户数据的加密层":::

1. 创建密钥后，选择新创建的密钥，然后选择其当前版本。

1. 复制密钥的密钥标识符（最后一个正斜杠之后的部分除外）：

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="围绕客户数据的加密层":::

## <a name="create-a-new-azure-cosmos-account"></a>创建新的 Azure Cosmos 帐户

### <a name="using-the-azure-portal"></a>使用 Azure 门户

当你从 Azure 门户创建新的 Azure Cosmos DB 帐户时，请选择“加密”步骤中“客户管理的密钥”。 在“密钥 URI”字段中，粘贴从上一步复制的 Azure Key Vault 密钥的 URI/密钥标识符：

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="围绕客户数据的加密层":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> 使用 Azure PowerShell

使用 PowerShell 创建新的 Azure Cosmos DB 帐户时：

- 在“PropertyObject”**** 中，传递前面在“keyVaultKeyUri”**** 属性下复制的 Azure Key Vault 密钥的 URI。

- 使用 2019-12-12**** 或更高版本作为 API 版本。

> [!IMPORTANT]
> 必须显式设置 `locations` 属性，才能通过客户管理的密钥成功创建帐户。

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

创建帐户后，可以通过提取 Azure Key Vault 密钥的 URI 来验证是否已启用客户管理的密钥：

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

通过 Azure 资源管理器模板创建新的 Azure Cosmos 帐户时：

- 在“属性”**** 对象中，传递前面在“keyVaultKeyUri”**** 属性下复制的 Azure Key Vault 密钥的 URI。

- 使用 2019-12-12**** 或更高版本作为 API 版本。

> [!IMPORTANT]
> 必须显式设置 `locations` 属性，才能通过客户管理的密钥成功创建帐户。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

使用以下 PowerShell 脚本部署模板：

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> 使用 Azure CLI

通过 Azure CLI 创建新的 Azure Cosmos 帐户时，传递前面在 `--key-uri` 参数下复制的 Azure Key Vault 密钥的 URI。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

创建帐户后，可以通过提取 Azure Key Vault 密钥的 URI 来验证是否已启用客户管理的密钥：

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="key-rotation"></a>密钥轮换

可以通过两种方式来轮换 Azure Cosmos 帐户使用的客户托管密钥。

- 创建当前在 Azure Key Vault 中使用的密钥的新版本：

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="围绕客户数据的加密层":::

- 通过更新帐户的密钥 URI，将当前使用的密钥与完全不同的密钥交换。 在 Azure 门户中转到你的 Azure Cosmos 帐户，然后在左侧菜单中选择“数据加密”：

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="围绕客户数据的加密层":::

    然后，将“密钥 URI”替换为要使用的新密钥，并选择“保存”：

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="围绕客户数据的加密层":::

    下面介绍如何在 PowerShell 中实现相同的结果：

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

可以在 24 小时后禁用以前的密钥或密钥版本，也可以在 [Azure Key Vault 审核日志](../key-vault/general/logging.md)不再显示该密钥或密钥版本 Azure Cosmos DB 中的活动之后禁用。
    
## <a name="error-handling"></a>错误处理。

在 Azure Cosmos DB 中使用客户托管密钥 (CMK) 时，如果存在任何错误，Azure Cosmos DB 会在响应中返回错误详细信息以及 HTTP 子状态代码。 可以使用此子状态代码来调试问题的根本原因。 请参阅 [Azure Cosmos DB 的 HTTP 状态代码](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)一文获取支持的 HTTP 子状态代码的列表。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>启用客户管理的密钥是否额外收费？

否，启用此功能不收取任何费用。

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>客户管理的密钥会对容量计划产生怎样的影响？

使用客户管理的密钥时，数据库操作使用的[请求单位](./request-units.md)会增加，以反映执行加密和解密数据所需的额外处理。 这可能会导致预配容量的利用率略高。 请参阅下表中的指南：

| 操作类型 | 请求单位增加 |
|---|---|
| 点读取（按 ID 获取项） | 每个操作 + 5% |
| 任何写入操作 | 每个操作 + 6%<br/>每个索引属性约为 + 0.06 RU |
| 查询、读取更改源或冲突源 | 每个操作 + 15% |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>哪些数据是通过客户管理的密钥加密的？

Azure Cosmos 帐户中存储的所有数据都将通过客户托管密钥加密，但以下元数据除外：

- Azure Cosmos DB [帐户、数据库和容器](./account-overview.md#elements-in-an-azure-cosmos-account)的名称

- [存储过程](./stored-procedures-triggers-udfs.md)的名称

- [索引策略](./index-policy.md)中声明的属性路径

- 容器的[分区键](./partitioning-overview.md)的值

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>现有的 Azure Cosmos 帐户是否支持客户管理的密钥？

此功能目前仅适用于新帐户。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>是否有计划支持比帐户级别密钥更精细的粒度？

目前没有，但正在考虑容器级别的密钥。

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>如何判断是否在 Azure Cosmos 帐户上启用了客户管理的密钥？

在 Azure 门户中，转到 Azure Cosmos 帐户，并在左侧菜单中查看“数据加密”项；如果此项存在，则在帐户中启用了客户管理的密钥：

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="围绕客户数据的加密层":::

还可以通过编程方式提取 Azure Cosmos 帐户的详细信息，并查找 `keyVaultKeyUri` 属性是否存在。 请参阅上面的方法，[在 PowerShell 中](#using-powershell)[使用 Azure CLI](#using-azure-cli). 实现此操作。

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>客户管理的密钥如何影响备份？

Azure Cosmos DB 将[定期自动备份](./online-backup-and-restore.md)存储在帐户中的数据。 此操作可备份加密的数据。 若要使用还原的备份，需要在备份时使用的加密密钥。 这意味着，没有执行任何吊销操作，并且备份时使用的密钥版本仍将保持启用状态。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何吊销加密密钥？

密钥吊销是通过禁用密钥的最新版本来完成的：

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="围绕客户数据的加密层":::

或者，若要从 Azure Key Vault 实例撤消所有密钥，可以删除授予 Azure Cosmos DB 主体的访问策略：

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="围绕客户数据的加密层":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>吊销客户管理的密钥后可执行哪些操作？

吊销加密密钥后，删除帐户是唯一可执行的操作。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Cosmos DB 中的数据加密](./database-encryption-at-rest.md)。
- 大概了解如何[安全访问 Cosmos DB 中的数据](secure-access-to-data.md)。
