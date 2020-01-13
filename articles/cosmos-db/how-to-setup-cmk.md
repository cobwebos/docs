---
title: 为 Azure Cosmos DB 帐户配置客户管理的密钥
description: 了解如何为 Azure Cosmos DB 帐户配置客户管理的密钥
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/11/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 964c3e4e2de43e6bcae353f0b525eb62e6613361
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911860"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>为 Azure Cosmos DB 帐户配置客户管理的密钥

> [!NOTE]
> 此时，你必须请求访问权限才能使用此功能。 为此，请联系[cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)。

存储在 Azure Cosmos DB 帐户中的数据会自动且无缝地加密。 Azure Cosmos DB 提供了两个选项用于管理用于加密静态数据的密钥：
- **服务托管的密钥**。 默认情况下，Microsoft 管理用于加密 Azure Cosmos DB 帐户的密钥。
- **客户托管的密钥（CMK）** 。 你可以选择使用所管理的密钥来添加第二层加密。

客户管理的密钥必须存储在[Azure Key Vault](../key-vault/key-vault-overview.md)中。 必须为每个启用 CMK 的帐户提供一个密钥，并使用该密钥来加密存储在该帐户中的所有数据。

## <a name="setup"></a>设置

目前，客户托管的密钥仅适用于新帐户，需要在帐户创建过程中设置。

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. 确保为你的 Azure 订阅注册 Azure Cosmos DB 资源提供程序

从 Azure 门户中转到 Azure 订阅，并从左侧菜单中选择 "**资源提供程序**"：

![左侧菜单中的 "资源提供程序" 条目](./media/how-to-setup-cmk/portal-rp.png)

搜索**Microsoft DocumentDB**资源提供程序。
- 如果资源提供程序已标记为已注册，则无需执行任何操作。
- 如果没有，请选择它并单击 "**注册**"：

    ![注册 Microsoft DocumentDB 资源提供程序](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. 配置 Azure Key Vault 实例

将客户托管的密钥用于 Azure Cosmos DB 需要在计划用于承载加密密钥的 Azure Key Vault 实例上设置两个属性：**软删除**和不**清除**。 默认情况下，这些属性不会启用，但可以使用 PowerShell 或 Azure CLI 启用。

若要了解如何在现有 Azure Key Vault 实例上启用这些属性，请参阅以下文章之一中标题为启用软删除和启用清除保护的部分：
- [如何将软删除与 PowerShell 配合使用](../key-vault/key-vault-soft-delete-powershell.md)
- [如何将软删除与 Azure CLI 一起使用](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. 将访问策略添加到 Azure Key Vault 实例

在 Azure 门户中，请前往你计划用于托管加密密钥的 Azure Key Vault 实例。 然后，从左侧菜单中选择 "**访问策略**"：

![左侧菜单中的 "访问策略"](./media/how-to-setup-cmk/portal-akv-ap.png)

- 选择 " **+ 添加访问策略**"
- 在 "**密钥权限**" 下拉列表菜单中，选择 "**获取**、**解包**密钥和**环绕关键字**"：

    ![选择正确的权限](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- 在 "**选择主体**" 下，选择 "**未**选择"。 然后，搜索并选择**Azure Cosmos DB**主体。 最后，单击底部的 "**选择**" （如果找不到**Azure Cosmos DB**主体，你可能需要在步骤1中重新注册**Microsoft DocumentDB**资源提供程序）：

    ![选择 Azure Cosmos DB 主体](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- 选择 "**添加**" 以添加新的访问策略

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. 在 Azure Key Vault 中生成密钥

在 Azure 门户中，请执行计划用来托管加密密钥的 Azure Key Vault 实例。 然后，从左侧菜单中选择 "**密钥**"：

![左侧菜单中的 "密钥" 项](./media/how-to-setup-cmk/portal-akv-keys.png)

- 选择**生成/导入**
- 提供新密钥的名称，选择 RSA 密钥大小（建议最少为3072以获得最佳安全性），然后选择 "**创建**"：

    ![创建新密钥](./media/how-to-setup-cmk/portal-akv-gen.png)

- 创建密钥后，单击新创建的密钥，并单击其当前版本
- 复制密钥的**密钥标识符**（最后一个正斜杠后的部分除外）：

    ![复制密钥的密钥标识符](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. 创建新的 Azure Cosmos DB 帐户

#### <a name="using-the-azure-portal"></a>使用 Azure 门户

从 Azure 门户创建新的 Azure Cosmos DB 帐户时，请在**加密**步骤中选择 "**客户管理的密钥**"。 在 "**密钥 URI** " 字段中，传递从步骤4中复制的 Azure Key Vault 密钥的 URI：

![在 Azure 门户中设置 CMK 参数](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>使用 PowerShell

使用 PowerShell 创建新的 Azure Cosmos DB 帐户时，
- 从**PropertyObject**中的**keyVaultKeyUri**属性下的步骤4中传递复制的 Azure Key Vault 密钥的 URI。
- 请确保使用 "2019-12-12" 作为 API 版本。

> [!IMPORTANT]
> 必须显式设置 `Location` 参数，才能通过 CMK 成功创建帐户。

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

#### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

通过 Azure 资源管理器模板创建新的 Azure Cosmos DB 帐户时：
- 在**properties**对象的**keyVaultKeyUri**属性下传递从步骤4中复制的 Azure Key Vault 密钥的 URI
- 请确保使用 "2019-12-12" 作为 API 版本

> [!IMPORTANT]
> 必须显式设置 `location` 参数，才能通过 CMK 成功创建帐户。

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

## <a name="frequently-asked-questions"></a>常见问题

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>使用客户管理的密钥时是否需要支付额外费用？

可以。 若要考虑使用客户管理的密钥管理数据加密和解密所需的额外计算负载，对 Azure Cosmos DB 帐户执行的所有操作都将在使用的[请求单位](./request-units.md)中增加25%。

### <a name="what-data-gets-encrypted-with-the-cmk"></a>哪些数据是通过 CMK 加密的？

Azure Cosmos DB 帐户中存储的所有数据都将用 CMK 加密，以下元数据除外：
- Azure Cosmos DB[帐户、数据库和容器](./account-overview.md#elements-in-an-azure-cosmos-account)的名称，
- [存储过程](./stored-procedures-triggers-udfs.md)的名称，
- [索引策略](./index-policy.md)中声明的属性路径，
- 容器的[分区键](./partitioning-overview.md)的值。

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>现有帐户是否支持客户托管的密钥？

此功能目前仅适用于新帐户。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>是否有计划支持比帐户级别密钥更精细的粒度？

目前不会考虑容器级别的键。

### <a name="how-does-customer-managed-keys-affect-backups"></a>客户托管的密钥如何影响备份？

Azure Cosmos DB 会[定期自动备份](./online-backup-and-restore.md)帐户中存储的数据。 此操作可备份加密的数据。 为了使还原的备份可用，备份时使用的加密密钥必须仍然可用。 这意味着，不应进行任何吊销，并且仍将启用备份时使用的密钥版本。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何实现撤销加密密钥吗？

密钥吊销是通过禁用密钥的最新版本来完成的：

![禁用密钥版本](./media/how-to-setup-cmk/portal-akv-rev2.png)

或者，若要从 Azure Key Vault 实例撤消所有密钥，可以删除授予 Azure Cosmos DB 主体的访问策略：

![正在删除 Azure Cosmos DB 主体的访问策略](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>撤消客户管理的密钥后哪些操作可用？

撤消加密密钥时，唯一可能的操作是删除帐户。

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure Cosmos DB 中的数据加密](./database-encryption-at-rest.md)
- 大致了解[Cosmos DB 中数据的安全访问](secure-access-to-data.md)