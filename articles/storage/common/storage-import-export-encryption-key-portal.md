---
title: 使用 Azure 门户为导入/导出服务配置客户管理的密钥
description: 了解如何使用 Azure 门户使用 Azure 导入/导出服务的 Azure Key Vault 来配置客户管理的密钥。 使用客户管理的密钥，你可以创建、轮换、禁用和撤销访问控制。
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d0a1826dafd1e6ce6202dc4f29417a1ce100e54f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195257"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>在导入/导出服务的 Azure Key Vault 中使用客户托管的密钥

Azure 导入/导出可通过加密密钥保护用于锁定驱动器的 BitLocker 密钥。 默认情况下，BitLocker 密钥使用 Microsoft 托管的密钥进行加密。 为了进一步控制加密密钥，还可以提供客户管理的密钥。

必须创建客户管理的密钥并将其存储在 Azure Key Vault 中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../../key-vault/general/overview.md)

本文介绍如何在[Azure 门户](https://portal.azure.com/)中通过导入/导出服务使用客户管理的密钥。

## <a name="prerequisites"></a>必备条件

在开始之前，请确保：

1. 已按照中的说明创建了导入或导出作业：

    - [为 Blob 创建导入作业](storage-import-export-data-to-blobs.md)。
    - [为文件创建导入作业](storage-import-export-data-to-files.md)。
    - [为 blob 创建导出作业](storage-import-export-data-from-blobs.md)

2. 你有一个现有的 Azure Key Vault，其中包含一个可用于保护 BitLocker 密钥的密钥。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户从 Azure Key Vault 设置和检索机密](../../key-vault/secrets/quick-create-portal.md)。

    - 在现有 Key Vault 上设置**软删除**和不**清除**。 默认情况下未启用这些属性。 若要启用这些属性，请参阅以下文章之一中标题为**启用软删除**和**启用清除保护**的部分：

        - [如何将软删除与 PowerShell 配合使用](../../key-vault/general/soft-delete-powershell.md)。
        - [如何通过 CLI 使用软删除](../../key-vault/general/soft-delete-cli.md)。
    - 现有密钥保管库的 RSA 密钥应为2048或更大。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的**Key Vault 密钥**。
    - 密钥保管库必须与你的数据的存储帐户位于同一区域。  
    - 如果没有现有 Azure Key Vault，还可以按照以下部分中所述，以内联方式创建该。

## <a name="enable-keys"></a>启用密钥

为导入/导出服务配置客户管理的密钥是可选的。 默认情况下，导入/导出服务使用 Microsoft 托管密钥来保护 BitLocker 密钥。 若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 对于导入作业，请参阅 "**概述**" 边栏选项卡。
2. 在右侧窗格中，选择 "**选择加密 BitLocker 密钥的方式**"。

    ![选择加密选项](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. 在 "**加密**" 边栏选项卡中，可以查看和复制设备 BitLocker 密钥。 在 "**加密类型**" 下，可以选择要保护 BitLocker 密钥的方式。 默认情况下，使用 Microsoft 托管密钥。

    ![查看 BitLocker 密钥](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 你可以选择指定客户管理的密钥。 选择客户托管密钥后，**选择 "密钥保管库" 和 "密钥**"。

    ![选择客户托管的密钥](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. 在**从 Azure Key Vault**边栏选项卡中选择 "密钥"，会自动填充订阅。 对于**密钥保管库**，可以从下拉列表中选择现有的密钥保管库。

    ![选择或创建 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. 你还可以选择 "**新建**" 以创建新的密钥保管库。 在 "**创建密钥保管库" 边栏选项卡**中，输入资源组和密钥保管库名称。 接受所有其他默认值。 选择“查看 + 创建”  。

    ![新建 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 查看与密钥保管库关联的信息，然后选择 "**创建**"。 等待几分钟，以便完成密钥保管库的创建。

    ![创建 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. 在 Azure Key Vault 的 "**选择密钥**" 中，可以选择现有密钥保管库中的密钥。

9. 如果创建了新的密钥保管库，请选择 "**新建**" 以创建一个密钥。 RSA 密钥大小可以是2048或更高。

    ![在 Azure Key Vault 中创建新密钥](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    如果在创建密钥保管库时未启用软删除和清除保护，则将更新密钥保管库以启用软删除和清除保护。

10. 提供密钥的名称，接受其他默认值，然后选择 "**创建**"。

    ![创建新密钥](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. 选择**版本**，然后选择 "**选择**"。 系统会通知你已在 key vault 中创建密钥。

    ![在密钥保管库中创建的新密钥](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

在 "**加密**" 边栏选项卡中，可以看到密钥保管库以及为客户托管密钥选择的密钥。

> [!IMPORTANT]
> 在导入/导出作业的任何阶段，只能禁用 Microsoft 托管密钥并移动到客户托管密钥。 但是，在创建客户托管密钥后，你无法将其禁用。

## <a name="troubleshoot-customer-managed-key-errors"></a>客户托管密钥错误疑难解答

如果收到与客户托管密钥相关的任何错误，请使用下表进行故障排除：

| 错误代码     |详细信息     | 性?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | 撤消对客户托管密钥的访问权限。                                                       | 是，检查： <ol><li>密钥保管库在访问策略中仍具有 MSI。</li><li>访问策略启用了 Get、Wrap 和解包权限。</li><li>如果密钥保管库位于防火墙后面的 VNet 中，请选中 "**允许 Microsoft 可信服务**已启用"。</li><li>检查作业资源的 MSI 是否已重置为 `None` 使用 api。<br>如果是，则将值设置回 `Identity = SystemAssigned` 。 这将重新创建作业资源的标识。<br>创建新标识后， `Get` `Wrap` `Unwrap` 为密钥保管库访问策略中的新标识启用、和权限</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | 禁用客户管理的密钥。                                         | 是，通过启用密钥版本     |
| CmkErrorKeyNotFound      | 找不到客户管理的密钥。 | 是的，如果已删除该密钥，但该密钥仍处于清除持续时间内，请[删除 "撤消密钥保管库密钥](https://docs.microsoft.com/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)"。<br>其它 <ol><li>是，如果客户已备份密钥并进行还原。</li><li>不，否则为。</li></ol>
| CmkErrorVaultNotFound |找不到客户托管密钥的密钥保管库。 |   如果已删除密钥保管库：<ol><li>如果是，则为 "是"，如果它处于 "清除保护" 持续时间，请使用[恢复密钥保管库](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步骤。</li><li>否，如果超出了清除保护持续时间。</li></ol><br>如果密钥保管库已迁移到其他租户，可以使用以下步骤之一进行恢复：<ol><li>将密钥保管库还原回旧租户。</li><li>设置 `Identity = None` ，然后将值设置回 `Identity = SystemAssigned` 。 创建新标识后，此操作将删除并重新创建标识。 `Get` `Wrap` `Unwrap` 对密钥保管库的访问策略中的新标识启用、和权限。</li></ol>|

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
