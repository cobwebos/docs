---
title: 为 Azure Cosmos DB 帐户配置客户管理的密钥
description: 了解如何通过 Azure Key Vault 为 Azure Cosmos DB 帐户配置客户管理的密钥
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 44bbd7eab80ecb1cbfef9738e42b4070dff31180
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77506045"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>为你的 Azure Cosmos 帐户配置客户托管密钥，Azure Key Vault

> [!NOTE]
> 此时，你必须请求访问权限才能使用此功能。 为此，请联系[azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)。

存储在 Azure Cosmos 帐户中的数据会自动且无缝地加密。 Azure Cosmos DB 提供了两个选项来管理用于加密静态数据的密钥：

- **服务托管的密钥**：默认情况下，Microsoft 管理用于对 Azure Cosmos 帐户中的数据进行加密的密钥。

- **客户托管的密钥（CMK）** ：可以选择使用自己的密钥来添加第二层加密。

必须将客户托管密钥存储在[Azure Key Vault](../key-vault/key-vault-overview.md)中，并为使用客户托管密钥启用的每个 Azure Cosmos 帐户提供密钥。 此密钥用于加密存储在该帐户中的所有数据。

> [!NOTE]
> 客户托管的密钥目前仅适用于新的 Azure Cosmos 帐户。 应在帐户创建过程中对其进行配置。

## <a id="register-resource-provider"></a>为你的 Azure 订阅注册 Azure Cosmos DB 资源提供程序

1. 登录到[Azure 门户](https://portal.azure.com/)，请访问 Azure 订阅，并在 "**设置**" 选项卡下选择 "**资源提供程序**"：

   ![左侧菜单中的 "资源提供程序" 条目](./media/how-to-setup-cmk/portal-rp.png)

1. 搜索**Microsoft DocumentDB**资源提供程序。 验证资源提供程序是否已标记为已注册。 如果不是，则选择资源提供程序，并选择 "**注册**"：

   ![注册 Microsoft DocumentDB 资源提供程序](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>配置 Azure Key Vault 实例

将客户托管的密钥与 Azure Cosmos DB 结合使用时，需要在计划用于承载加密密钥的 Azure Key Vault 实例上设置两个属性。 这些属性包括**软删除**和不**清除**。 默认情况下不启用这些属性。 可以使用 PowerShell 或 Azure CLI 来启用它们。

若要了解如何在现有 Azure Key Vault 实例上启用这些属性，请参阅以下文章之一中的 "启用软删除" 和 "启用清除保护" 部分：

- [如何将软删除与 PowerShell 配合使用](../key-vault/key-vault-soft-delete-powershell.md)
- [如何将软删除与 Azure CLI 一起使用](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>将访问策略添加到 Azure Key Vault 实例

1. 在 Azure 门户中，请参阅你计划用于承载加密密钥的 Azure Key Vault 实例。 从左侧菜单中选择 "**访问策略**"：

   ![左侧菜单中的 "访问策略"](./media/how-to-setup-cmk/portal-akv-ap.png)

1. 选择 " **+ 添加访问策略**"。

1. 在 "**密钥权限**" 下拉菜单下，选择 "**获取**"、"**解包密钥**" 和 "**包装密钥**" 权限：

   ![选择正确的权限](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. 在 "**选择主体**" 下，选择 "**未**选择"。 然后，搜索 " **Azure Cosmos DB**主体" 并选择它（为了更容易查找，还可以按主体 id： `a232010e-820c-4083-83bb-3ace5fc29d0b` 用于任何 azure 区域，其中，主体 id 是 `57506a73-e302-42a9-b869-6f12d9ec29e9`的 azure 政府区域除外）。 最后，选择底部的 "**选择**"。 如果**Azure Cosmos DB**主体不在列表中，则可能需要重新注册**Microsoft DocumentDB**资源提供程序，如本文的[注册资源提供程序](#register-resource-provider)部分中所述。

   ![选择 Azure Cosmos DB 主体](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. 选择 "**添加**" 以添加新的访问策略。

## <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中生成密钥

1. 在 Azure 门户中，请执行你计划用于承载加密密钥的 Azure Key Vault 实例。 然后，从左侧菜单中选择 "**密钥**"：

   ![左侧菜单中的 "密钥" 项](./media/how-to-setup-cmk/portal-akv-keys.png)

1. 选择 "**生成/导入**"，为新密钥提供名称，并选择 RSA 密钥大小。 建议至少使用3072以获得最佳安全性。 然后选择“创建”：

   ![新建密钥](./media/how-to-setup-cmk/portal-akv-gen.png)

1. 创建密钥后，选择新创建的密钥，然后选择当前版本。

1. 复制密钥的**密钥标识符**（最后一个正斜杠后面的部分除外）：

   ![复制密钥的密钥标识符](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>创建新的 Azure Cosmos 帐户

### <a name="using-the-azure-portal"></a>使用 Azure 门户

从 Azure 门户创建新的 Azure Cosmos DB 帐户时，请在**加密**步骤中选择 "**客户管理的密钥**"。 在 "**密钥 URI** " 字段中，粘贴从上一步复制的 Azure Key Vault 密钥的 URI/密钥标识符：

![在 Azure 门户中设置 CMK 参数](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

使用 PowerShell 创建新的 Azure Cosmos DB 帐户时：

- 传递前面在**PropertyObject**的**keyVaultKeyUri**属性下复制的 Azure Key Vault 密钥的 URI。

- 使用**2019-12-12**作为 API 版本。

> [!IMPORTANT]
> 必须显式设置 `Location` 参数，才能通过客户托管的密钥成功创建帐户。

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

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

通过 Azure 资源管理器模板创建新的 Azure Cosmos 帐户时：

- 传递先前在**properties**对象中的**keyVaultKeyUri**属性下复制的 Azure Key Vault 密钥的 URI。

- 使用**2019-12-12**作为 API 版本。

> [!IMPORTANT]
> 必须显式设置 `Location` 参数，才能通过客户托管的密钥成功创建帐户。

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

用以下 PowerShell 脚本部署模板：

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

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>使用客户管理的密钥是否需要支付额外费用？

是的。 若要考虑使用客户管理的密钥管理数据加密和解密所需的额外计算负载，对 Azure Cosmos 帐户执行的所有操作都消耗25% 的[请求单位](./request-units.md)。

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>哪些数据是用客户管理的密钥加密的？

Azure Cosmos 帐户中存储的所有数据都是用客户管理的密钥加密的，以下元数据除外：

- Azure Cosmos DB[帐户、数据库和容器](./account-overview.md#elements-in-an-azure-cosmos-account)的名称

- [存储过程](./stored-procedures-triggers-udfs.md)的名称

- [索引策略](./index-policy.md)中声明的属性路径

- 容器的[分区键](./partitioning-overview.md)的值

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>现有的 Azure Cosmos 帐户是否支持客户托管的密钥？

此功能当前仅适用于新帐户。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>是否有计划支持比帐户级别密钥更精细的粒度？

目前不会考虑容器级别的键。

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>客户管理的密钥如何影响备份？

Azure Cosmos DB 会[定期自动备份](./online-backup-and-restore.md)帐户中存储的数据。 此操作可备份加密的数据。 若要使用还原的备份，则需要在备份时使用的加密密钥。 这意味着，不会进行任何吊销，并且仍将启用备份时使用的密钥版本。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何实现撤销加密密钥吗？

密钥吊销是通过禁用密钥的最新版本来完成的：

![禁用密钥版本](./media/how-to-setup-cmk/portal-akv-rev2.png)

或者，若要从 Azure Key Vault 实例撤消所有密钥，可以删除授予 Azure Cosmos DB 主体的访问策略：

![正在删除 Azure Cosmos DB 主体的访问策略](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>撤消客户管理的密钥后哪些操作可用？

撤消加密密钥时，唯一可能的操作是删除帐户。

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure Cosmos DB 中的数据加密](./database-encryption-at-rest.md)。
- 大致了解[Cosmos DB 中数据的安全访问](secure-access-to-data.md)。
