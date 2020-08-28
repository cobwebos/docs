---
title: 为 Azure Batch 帐户配置 Azure Key Vault 和托管标识的客户托管密钥
description: 了解如何使用密钥加密批数据
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: 35780f915247e88a5de093594b653ddcebdfb06b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008873"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>为 Azure Batch 帐户配置 Azure Key Vault 和托管标识的客户托管密钥

默认情况下 Azure Batch 使用平台管理的密钥来加密存储在 Azure Batch 服务中的所有客户数据，如证书、作业/任务元数据。 （可选）可以使用自己的密钥（即客户托管的密钥）来加密存储在 Azure Batch 中的数据。

提供的密钥必须在 [Azure Key Vault](../key-vault/general/basic-concepts.md)中生成，并且要使用 [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md)启用要使用客户托管的密钥配置的批处理帐户。

> [!IMPORTANT]
> Azure Batch 当前正在公开预览版中的客户托管密钥西欧、北欧、瑞士北部、美国中南部、美国中南部、美国西部、美国东部、美国东部2、美国西部2、US Gov 弗吉尼亚州和 US Gov 亚利桑那州地区。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>使用系统分配的托管标识创建 Batch 帐户

### <a name="azure-portal"></a>Azure 门户

在[Azure 门户](https://portal.azure.com/)中创建 Batch 帐户时，请在 "**高级**" 选项卡下的 "标识类型" 中选择 "**系统分配**"。

![新批处理帐户的系统分配的标识类型](./media/batch-customer-managed-key/create-batch-account.png)

创建帐户后，可以在**属性**部分下的 "**标识主体 id** " 字段中找到唯一的 GUID。 将显示 **标识类型** `SystemAssigned` 。

![标识主体 id 字段中的唯一 GUID](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>Azure CLI

创建新的 Batch 帐户时，请 `SystemAssigned` 为参数指定 `--identity` 。

```powershell
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

创建帐户后，你可以验证是否已在此帐户上启用系统分配的托管标识。 请务必注意 `PrincipalId` ，因为此值将需要授予此批处理帐户对 Key Vault 的访问权限。

```powershell
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> 在 Batch 帐户中创建的系统分配的托管标识仅用于从 Key Vault 检索客户管理的密钥。 此标识在 Batch 池上不可用。

## <a name="configure-your-azure-key-vault-instance"></a>配置 Azure Key Vault 实例

### <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

使用 Azure Batch 的客户托管密钥创建 Azure Key Vault 实例时，请确保已启用 **软删除** 和 **清除保护** 。

![Key Vault 创建屏幕](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>将访问策略添加到 Azure Key Vault 实例

在 Key Vault Azure 门户的 "设置" 下的 " **访问策略** " 中，在 " **设置**" 下，添加使用托管标识的 Batch 帐户访问权限。 在 " **密钥权限**" 下，选择 " **获取**"、" **换行** 并 **解包**密钥"。 

![添加访问策略](./media/batch-customer-managed-key/key-permissions.png)

在 "**主体**" 下的 "**选择**" 字段中，填写 `principalId` 前面检索的，或者填写批处理帐户的名称。

![输入 principalId](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中生成密钥

在 Azure 门户中，在 " **密钥** " 部分中转到 Key Vault 实例，然后选择 " **生成/导入**"。 选择要作为的 **密钥类型** `RSA` ，将 **RSA 密钥大小** 至少为 `2048` 位。 `EC` 当前不支持将密钥类型作为 Batch 帐户上的客户托管密钥。

![创建密钥](./media/batch-customer-managed-key/create-key.png)

创建密钥后，单击新创建的密钥和当前版本，在 "**属性**" 部分中复制**密钥标识符**。  请确保选中 " **允许的操作**" 下的 " **自动换行** " 和 " **解包密钥** "。

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>在 Azure Batch 帐户上启用客户管理的密钥

### <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com/)中，请切换到 "Batch 帐户" 页。 在 " **加密** " 部分下，启用 **客户管理的密钥**。 你可以直接使用密钥标识符，也可以选择密钥保管库，然后单击 " **选择密钥保管库和密钥**"。

![在 "加密" 下，启用客户管理的密钥](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

使用系统分配的托管标识创建批处理帐户并授予对 Key Vault 的访问权限后，请使用参数下的 URL 更新批处理帐户 `{Key Identifier}` `keyVaultProperties` 。 还将 **encryption_key_source** 设置为 `Microsoft.KeyVault` 。

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>更新客户托管的密钥版本

当你创建密钥的新版本时，请更新 Batch 帐户以使用新版本。 执行以下步骤：

1. 在 Azure 门户中导航到 Batch 帐户，并显示加密设置。
2. 输入新密钥版本的 URI。 或者，可以再次选择 Key Vault 和密钥以更新版本。
3. 保存所做更改。

你还可以使用 Azure CLI 来更新版本。

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>使用不同的密钥进行批处理加密

若要更改用于批处理加密的密钥，请执行以下步骤：

1. 导航到 Batch 帐户并显示加密设置。
2. 输入新密钥的 URI。 也可选择密钥保管库并选择一个新密钥。
3. 保存所做更改。

你还可以使用 Azure CLI 来使用不同的密钥。

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>常见问题
  * **现有批处理帐户是否支持客户托管的密钥？** 不行。 仅新的批处理帐户支持客户管理的密钥。
  * **能否选择大于2048位的 RSA 密钥大小？** 是的，还支持 RSA 密钥大小 `3072` 和 `4096` 位数。
  * **吊销客户管理的密钥后可执行哪些操作？** 如果批处理失去了对客户管理的密钥的访问权限，则唯一允许的操作是帐户删除。
  * **如果意外删除了 Key Vault 的密钥，应该如何还原批处理帐户的访问权限？** 由于已启用清除保护和软删除，因此可以还原现有密钥。 有关详细信息，请参阅 [恢复 Azure Key Vault](../key-vault/general/soft-delete-cli.md#recovering-a-key-vault)。
  * **能否禁用客户管理的密钥？** 你可以随时将批处理帐户的加密类型设置回 "Microsoft 托管密钥"。 此后，可以随意删除或更改该密钥。
  * **如何轮换密钥？** 客户管理的密钥不会自动轮替。 若要轮换密钥，请更新与帐户相关联的密钥标识符。
  * **还原访问权限后，批处理帐户再次工作需要多长时间？** 还原访问后，最多可能需要10分钟的时间才能访问该帐户。
  * **虽然批处理帐户不可用，但我的资源会发生什么情况呢？** 当 Batch 访问客户托管密钥时，正在运行的所有池将继续运行。 但是，节点将转换为不可用状态，并且任务将停止运行 (，并) 重新排队。 还原访问权限后，节点将再次变得可用，任务将重新启动。
  * **此加密机制是否适用于批处理池中的 VM 磁盘？** 不行。 对于云服务配置池，不对 OS 和临时磁盘应用加密。 对于虚拟机配置池，默认情况下，将使用 Microsoft 平台托管密钥对 OS 和任何指定的数据磁盘进行加密。 目前，不能为这些磁盘指定您自己的密钥。 若要使用 Microsoft 平台托管密钥为批处理池加密 Vm 的临时磁盘，必须在[虚拟机配置](/rest/api/batchservice/pool/add#virtualmachineconfiguration)池中启用[diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration)属性。 对于高度敏感的环境，我们建议启用临时磁盘加密，避免将敏感数据存储在 OS 和数据磁盘上。 有关详细信息，请参阅 [创建启用了磁盘加密的池](./disk-encryption.md)
  * **系统分配的托管标识在计算节点上可用的批处理帐户上吗？** 不行。 此托管标识目前仅用于访问客户托管的密钥的 Azure Key Vault。
  
