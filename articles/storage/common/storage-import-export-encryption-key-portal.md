---
title: 使用 Azure 门户为导入/导出服务配置客户管理的密钥
description: 了解如何使用 Azure 门户通过 Azure Key Vault 为 Azure 导入/导出服务配置客户管理的密钥。 使用客户管理的密钥可以创建、轮换、禁用和撤销访问控制。
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d0a1826dafd1e6ce6202dc4f29417a1ce100e54f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "83195257"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>将 Azure Key Vault 中的客户管理的密钥用于导入/导出服务

Azure 导入/导出使用加密密钥保护用于锁定驱动器的 BitLocker 密钥。 默认情况下将使用 Microsoft 管理的密钥对 BitLocker 密钥进行加密。 为了更进一步控制加密密钥，你还可以提供客户管理的密钥。

必须在 Azure Key Vault 中创建并存储客户管理的密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)

本文介绍了如何在 [Azure 门户](https://portal.azure.com/)中将客户管理的密钥用于导入/导出服务。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你已按照以下部分中的说明创建了一个导入或导出作业：

    - [创建适用于 blob 的导入作业](storage-import-export-data-to-blobs.md)。
    - [创建适用于文件的导入作业](storage-import-export-data-to-files.md)。
    - [创建适用于 blob 的导出作业](storage-import-export-data-from-blobs.md)

2. 你有一个现有的 Azure Key Vault，其中包含可用于保护 BitLocker 密钥的密钥。 若要了解如何使用 Azure 门户创建 Key Vault，请参阅[快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索机密](../../key-vault/secrets/quick-create-portal.md)。

    - 在现有 Key Vault 上设置了“软删除”和“不清除”。 默认情况下未启用这些属性。 若要启用这些属性，请参阅以下文章之一中标题为“启用软删除”和“启用清除保护”的部分： 

        - [如何通过 PowerShell 使用软删除](../../key-vault/general/soft-delete-powershell.md)。
        - [如何通过 CLI 使用软删除](../../key-vault/general/soft-delete-cli.md)。
    - 现有密钥保管库应当具有大小为 2048 或更大的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的“Key Vault 密钥”。
    - 密钥保管库必须与数据的存储帐户位于同一区域。  
    - 如果你没有现有的 Azure Key Vault，也可按照以下部分的描述以内联方式创建它。

## <a name="enable-keys"></a>启用密钥

为导入/导出服务配置客户管理的密钥这一操作是可选的。 默认情况下，导入/导出服务使用 Microsoft 管理的密钥来保护 BitLocker 密钥。 若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 转到导入作业的“概览”边栏选项卡。
2. 在右侧窗格中，选择“选择 BitLocker 密钥的加密方式”。

    ![选择加密选项](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. 在“加密”边栏选项卡中，可以查看和复制设备 BitLocker 密钥。 在“加密类型”下，可以选择要如何保护你的 BitLocker 密钥。 默认情况下将使用 Microsoft 管理的密钥。

    ![查看 BitLocker 密钥](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. 你可以选择指定客户管理的密钥。 选择客户管理的密钥后，单击“选择密钥保管库和密钥”。

    ![选择客户管理的密钥](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. 在“从 Azure Key Vault 中选择密钥”边栏选项卡中，订阅会自动填充。 对于“密钥保管库”，可以从下拉列表中选择现有的密钥保管库。

    ![选择或创建 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. 还可以选择“新建”来创建新的密钥保管库。 在“创建密钥保管库”边栏选项卡中，输入资源组和密钥保管库名称。 接受其他所有默认值。 选择“查看 + 创建”。

    ![创建新的 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. 查看与密钥保管库关联的信息，然后选择“创建”。 等待几分钟，以便完成密钥保管库的创建。

    ![创建 Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. 在“从 Azure Key Vault 中选择密钥”中，可以选择现有密钥保管库中的密钥。

9. 如果你创建了新的密钥保管库，请选择“新建”来创建密钥。 RSA 密钥大小可以是 2048 或更大。

    ![在 Azure Key Vault 中创建新密钥](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    如果在创建密钥保管库时未启用软删除和清除保护，则会更新密钥保管库以启用软删除和清除保护。

10. 提供密钥的名称，接受其他默认值，然后选择“创建”。

    ![创建新密钥](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. 选择“版本”，然后选择“选择”。 系统会通知你已在密钥保管库中创建了密钥。

    ![在密钥保管库中创建的新密钥](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

在“加密”边栏选项卡中，可以看到为客户管理的密钥选择的密钥保管库和密钥。

> [!IMPORTANT]
> 在导入/导出作业的任何阶段，你只能禁用 Microsoft 管理的密钥，改为使用客户管理的密钥。 但是，在创建客户管理的密钥后，你无法将其禁用。

## <a name="troubleshoot-customer-managed-key-errors"></a>排查客户管理的密钥的错误

如果收到与客户管理的密钥相关的任何错误，请使用下表进行故障排除：

| 错误代码     |详细信息     | 可恢复？    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | 撤销了对客户管理的密钥的访问权限。                                                       | 是，检查以下事项： <ol><li>密钥保管库在访问策略中是否仍具有 MSI。</li><li>访问策略是否启用了获取、包装和解包权限。</li><li>如果密钥保管库位于防火墙后面的 VNet 中，请检查是否启用了“允许 Microsoft 信任的服务”。</li><li>使用 API 检查作业资源的 MSI 是否已重置为 `None`。<br>如果是，则将该值设置回 `Identity = SystemAssigned`。 这将重新创建作业资源的标识。<br>创建新标识后，在密钥保管库的访问策略中为新标识启用 `Get`、`Wrap` 和 `Unwrap` 权限</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | 禁用了客户管理的密钥。                                         | 是，通过启用密钥版本     |
| CmkErrorKeyNotFound      | 找不到客户管理的密钥。 | 是，如果该密钥已删除，但仍处于清除保护期内，请使用[撤消密钥保管库密钥删除](https://docs.microsoft.com/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)。<br>否则， <ol><li>如果客户已备份密钥并还原了它，则可恢复。</li><li>其他情况下，无法恢复。</li></ol>
| CmkErrorVaultNotFound |找不到客户管理的密钥的密钥保管库。 |   如果密钥保管库已删除：<ol><li>是，如果它处于清除保护期内，请使用[恢复密钥保管库](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步骤。</li><li>否，如果超出了清除保护期。</li></ol><br>如果密钥保管库已迁移到其他租户，可以使用以下步骤之一进行恢复：<ol><li>将密钥保管库还原回旧租户。</li><li>设置 `Identity = None`，然后将值设置回 `Identity = SystemAssigned`。 这将在新标识创建后删除并重新创建该标识。 在密钥保管库的“访问策略”中为新标识启用 `Get`、`Wrap` 和 `Unwrap` 权限。</li></ol>|

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
