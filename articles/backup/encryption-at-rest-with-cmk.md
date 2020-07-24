---
title: 使用客户托管密钥加密备份数据
description: 了解 Azure 备份如何允许使用客户管理的密钥（CMK）对备份数据进行加密。
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c26466582cbe5a10610f6766160c2b0bc51a4828
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091090"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客户托管密钥加密备份数据

Azure 备份允许使用客户管理的密钥（CMK）（而不是默认情况下启用的平台托管密钥）对备份数据进行加密。 用于加密备份数据的密钥必须存储在[Azure Key Vault](../key-vault/index.yml)中。

用于加密备份的加密密钥可能与用于源的加密密钥不同。 使用基于 AES 256 的数据加密密钥（DEK）保护数据，而使用密钥（KEK）对其进行保护。 这使你可以完全控制数据和密钥。 若要允许加密，需要向恢复服务保管库授予对 Azure 密钥保管库中加密密钥的访问权限。 你可以在需要时将密钥更改为和。

本文讨论以下内容：

- 创建恢复服务保管库
- 将恢复服务保管库配置为使用客户管理的密钥加密备份数据
- 对使用客户管理的密钥加密的保管库执行备份
- 从备份还原数据

## <a name="before-you-start"></a>开始之前

- 此功能仅允许加密**新的恢复服务保管库**。 不支持任何包含注册或试图注册到它的现有项目的保管库。

- 为恢复服务保管库启用后，使用客户管理的密钥的加密不能恢复为使用平台托管密钥（默认）。 你可以根据需要更改加密密钥。

- 此功能当前**不支持使用 MARS 代理进行备份**，并且你可能无法使用 CMK 加密的保管库。 MARS 代理使用基于用户密码的加密。 此功能也不支持经典 Vm 的备份。

- 此功能与[Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)无关，后者使用 BitLocker （适用于 Windows）和 DM dm-crypt （适用于 Linux）对 VM 磁盘进行基于来宾的加密

- 只能使用存储在位于**同一区域**中的 Azure Key Vault 中的密钥来加密恢复服务保管库。 而且，密钥必须仅为**RSA 2048 密钥**，并且应处于**启用**状态。

- 当前不支持跨资源组和订阅移动 CMK 加密恢复服务保管库。

- 此功能当前仅可通过 Azure 门户进行配置。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>将保管库配置为使用客户托管的密钥进行加密

本部分包括以下步骤：

1. 为恢复服务保管库启用托管标识

1. 将权限分配给保管库以访问 Azure Key Vault 中的加密密钥

1. 启用软删除和清除保护 Azure Key Vault

1. 将加密密钥分配到恢复服务保管库

必须按照上述顺序执行所有这些步骤，以达到预期结果。 下面将详细介绍每个步骤。

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>为恢复服务保管库启用托管标识

Azure 备份使用系统分配的托管标识对恢复服务保管库进行身份验证，以访问存储在 Azure Key Vault 中的加密密钥。 若要为恢复服务保管库启用托管标识，请按照下面所述的步骤进行操作。

>[!NOTE]
>启用后，不能禁用托管标识（即使暂时这样做）。 禁用托管标识可能导致出现不一致的行为。

1. 请参阅恢复服务保管库->**标识**

    ![标识设置](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. 将“状态”更改为“启用”，然后单击“保存”  。

1. 系统将生成一个对象 ID，该 ID 是保管库的系统分配的托管标识。

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>分配对恢复服务保管库的权限，以访问 Azure Key Vault 中的加密密钥

你现在需要允许恢复服务保管库访问包含加密密钥的 Azure Key Vault。 这是通过允许恢复服务保管库的托管标识访问 Key Vault 来完成的。

1. 请访问 Azure Key Vault >**访问策略**。 继续执行 **+ 添加访问策略**。

    ![添加访问策略](./media/encryption-at-rest-with-cmk/access-policies.png)

1. 在 "**密钥权限**" 下，选择 "**获取**"、"**列出**"、"**解包**密钥并**打包密钥**" 操作 这将指定对允许的键的操作。

    ![分配密钥权限](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. 请在搜索框中，**选择 "主体**"，并使用其名称或托管标识搜索你的保管库。 显示后，选择保管库，并单击窗格底部的 "**选择**"。

    ![选择主体](./media/encryption-at-rest-with-cmk/select-principal.png)

1. 完成后，单击 "**添加**" 以添加新的访问策略。

1. 单击 "**保存**" 以保存对 Azure Key Vault 的访问策略所做的更改。

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>启用软删除和清除保护 Azure Key Vault

需要在存储加密密钥的 Azure Key Vault 上**启用软删除和清除保护**。 可以从 Azure Key Vault UI 执行此操作，如下所示。 （或者，可以在创建 Key Vault 时设置这些属性）。 [在此处](../key-vault/general/overview-soft-delete.md)了解有关这些 Key Vault 属性的详细信息。

![启用软删除和清除保护](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

还可以使用以下步骤通过 PowerShell 启用软删除和清除保护：

1. 登录到你的 Azure 帐户。

    ```azurepowershell
    Login-AzAccount
    ```

1. 选择包含保管库的订阅。

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. 启用软删除

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. 启用清除保护

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>将加密密钥分配给 RS vault

>[!NOTE]
> 继续操作之前，请确保以下各项：
>
> - 上述所有步骤均已成功完成：
>   - 恢复服务保管库的托管标识已启用，并且已分配有必需的权限
>   - 已启用软删除和清除保护的 Azure Key Vault
> - 要为其启用 CMK 加密的恢复服务保管库没有任何受保护的项或未向其注册的项

确保了上述各项后，请继续选择保管库的加密密钥。

分配密钥：

1. 请参阅恢复服务保管库->**属性**

    ![加密设置](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. 单击 "**加密设置**" 下的 "**更新**"。

1. 在 "加密设置" 窗格中，选择 "**使用你自己的密钥**"，然后使用以下方法之一继续指定密钥。 **确保要使用的密钥是 RSA 2048 密钥，它处于启用状态。**

    1. 输入要用于对此恢复服务保管库中的数据进行加密的**密钥 URI** 。 还需要指定存在 Azure Key Vault （其中包含此密钥）的订阅。 可以从 Azure Key Vault 中的相应密钥获取此密钥 URI。 请确保正确复制了密钥 URI。 建议使用与密钥标识符一起提供的 "**复制到剪贴板**" 按钮。

        ![输入密钥 URI](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. 从 "密钥选取器" 窗格中的 Key Vault 浏览并选择密钥。

        ![从密钥保管库中选择密钥](./media/encryption-at-rest-with-cmk/key-vault.png)

1. 单击“保存” 。

1. **跟踪加密密钥更新的进度：** 可以使用恢复服务保管库中的**活动日志**跟踪密钥分配的进度。 状态应更改为 "**成功**"。 现在，保管库会将具有指定密钥的所有数据加密为 KEK。

    ![跟踪活动日志的进度](./media/encryption-at-rest-with-cmk/activity-log.png)

    ![状态成功](./media/encryption-at-rest-with-cmk/status-succeeded.png)

>[!NOTE]
> 当你希望更新/更改加密密钥时，此过程保持不变。 如果要更新和使用其他 Key Vault （与当前使用的密钥不同）中的密钥，请确保：
>
> - Key Vault 与恢复服务保管库位于同一区域
>
> - Key vault 已启用软删除和清除保护
>
> - 恢复服务保管库具有访问 Key Vault 所需的权限。

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>备份到使用客户管理的密钥加密的保管库

在继续配置保护之前，强烈建议您确保遵守以下清单。 这一点很重要，因为一旦将项目配置为备份（或试图配置）到非 CMK 加密的保管库，则无法在其上启用使用客户托管密钥的加密，并且它将继续使用平台管理的密钥。

>[!IMPORTANT]
> 在继续配置保护之前，必须已**成功**完成以下步骤：
>
>1. 已启用你的订阅，以便对你的备份保管库使用客户管理的密钥。
>1. 已创建备份保管库
>1. 已启用备份保管库的系统分配的托管标识
>1. 已分配对备份保管库的权限，以访问 Key Vault 中的加密密钥
>1. 已对 Key Vault 启用软删除和清除保护
>1. 为备份保管库分配了有效的加密密钥
>
>如果已确认以上所有步骤，则只需继续配置备份。

使用客户管理的密钥来配置和执行备份到恢复服务保管库的过程与使用平台托管密钥的保管库相同，**不会更改体验**。 这适用于[Azure vm 的备份](./quick-backup-vm-portal.md)以及在 VM 内运行的工作负荷（例如[SAP HANA](./tutorial-backup-sap-hana-db.md)、 [SQL Server](./tutorial-sql-backup.md)数据库）的备份。

## <a name="restoring-data-from-backup"></a>从备份还原数据

### <a name="vm-backup"></a>VM 备份

恢复服务保管库中存储的数据可以根据[此处](./backup-azure-arm-restore-vms.md)所述的步骤还原。 从使用客户管理的密钥加密的恢复服务保管库还原时，可以选择使用磁盘加密集（DES）加密还原的数据。

#### <a name="restoring-vm--disk"></a>正在还原 VM/磁盘

1. 从 "快照" 恢复点恢复磁盘/VM 时，将使用用于对源 VM 磁盘进行加密的 DES 来加密还原的数据。

1. 使用恢复类型为 "保管库" 的恢复点还原磁盘/VM 时，可以选择使用还原时指定的 DES 加密还原的数据。 或者，你可以选择继续在不指定 DES 的情况下还原数据，在这种情况下，将使用 Microsoft 托管密钥对其进行加密。

还原完成后，可以加密还原的磁盘/VM，而不考虑启动还原时所做的选择。

![还原点](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>从保管库恢复点还原时选择磁盘加密集

磁盘加密集在 "还原" 窗格的 "加密设置" 下指定，如下所示：

1. 在 "**使用你的密钥加密磁盘**" 中，选择 **"是"**。

1. 从下拉列表中，选择要用于已还原磁盘的 DES。 **确保你有权访问 DES。**

>[!NOTE]
>如果正在还原使用 Azure 磁盘加密的 VM，则在还原时选择 DES 的功能将不可用。

![使用密钥加密磁盘](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

#### <a name="restoring-files"></a>还原文件

执行文件还原时，还原的数据将使用用于对目标位置进行加密的密钥进行加密。

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>在 Azure Vm 中还原 SAP HANA/SQL 数据库

从在 Azure VM 中运行的备份 SAP HANA/SQL 数据库还原时，还原的数据将使用目标存储位置中使用的加密密钥进行加密。 它可以是客户托管的密钥或用于加密 VM 磁盘的平台托管密钥。

## <a name="frequently-asked-questions"></a>常见问题解答

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>是否可以使用客户管理的密钥加密现有的备份保管库？

不能，只能为新保管库启用 CMK 加密。 因此，保管库决不能保护任何项目。 事实上，在使用客户管理的密钥启用加密之前，不会尝试保护保管库中的任何项。

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>我尝试保护我的保管库中的某一项，但失败，并且该保管库仍未包含任何受保护的项。 能否为此保管库启用 CMK 加密？

不可以，保管库在过去不能尝试保护任何项。

### <a name="i-have-a-vault-that-is-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>我有一个使用 CMK 加密的保管库。 以后是否可以使用平台托管密钥恢复到加密，即使已将备份项保护到保管库？

否，启用 CMK 加密后，无法将其还原为使用平台管理的密钥。 你可以根据要求更改所用的密钥。

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Azure 备份的 CMK 加密是否也适用于 Azure Site Recovery？

不能，本文仅讨论备份数据的加密。 对于 Azure Site Recovery，需要将属性从服务中单独设置为可用。

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>我漏掉了本文中的一个步骤，并继续对数据源进行了保护。 我是否仍然可以使用 CMK 加密？

不遵循本文中的步骤并继续保护项目可能导致保管库无法使用客户管理的密钥来加密。 因此，建议您在继续保护项目之前引用[此核对清单](#backing-up-to-a-vault-encrypted-with-customer-managed-keys)。

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>使用 CMK 加密会增加备份的成本吗？

使用 CMK 加密进行备份不会给你带来任何额外的费用。 但是，你可能会继续产生使用 Azure Key Vault 存储密钥的费用。

## <a name="next-steps"></a>后续步骤

- [Azure 备份中的安全功能概述](security-overview.md)
