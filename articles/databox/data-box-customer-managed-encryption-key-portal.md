---
title: 使用 Azure 门户为 Azure Data Box 配置客户管理的密钥
description: 了解如何使用 Azure 门户通过 Azure 密钥保管库来为 Azure Data Box 配置客户管理的密钥。 使用客户管理的密钥可以创建、轮换、禁用和撤销访问控制。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 4bcd8deef28f8e0123e6e2171b3ab24d6ac49292
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84634993"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>将 Azure 密钥保管库中的客户管理的密钥用于 Azure Data Box

Azure Data Box 可通过加密密钥保护用于锁定设备的设备解锁密钥（也称为设备密码）。 默认情况下，使用 Microsoft 管理的密钥加密 Data Box 订单的设备解锁密钥。 如需进一步控制设备解锁密钥，还可以提供客户管理的密钥。 

必须在 Azure Key Vault 中创建并存储客户管理的密钥。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../key-vault/general/overview.md)。

本文演示如何在 [Azure 门户](https://portal.azure.com/)中将客户管理的密钥用于 Azure Data Box。 本文适用于 Azure Data Box 设备和 Azure Data Box Heavy 设备。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你已按照以下文章中的说明创建了 Azure Data Box 订单：[教程：订购 Azure Data Box](data-box-deploy-ordered.md)。

2. 你有一个现有的 Azure 密钥保管库，其中包含可用于保护设备解锁密钥的密钥。 若要了解如何使用 Azure 门户创建 Key Vault，请参阅[快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索机密](../key-vault/secrets/quick-create-portal.md)。

    - 现有密钥保管库上已设置“软删除”和“不清除”。 默认情况下未启用这些属性。 若要启用这些属性，请参阅以下文章之一中标题为“启用软删除”和“启用清除保护”的部分： 

        - [如何通过 PowerShell 使用软删除](../key-vault/general/soft-delete-powershell.md)。
        - [如何通过 CLI 使用软删除](../key-vault/general/soft-delete-cli.md)。
    - 现有密钥保管库应当具有大小为 2048 或更大的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure 密钥保管库密钥](../key-vault/keys/about-keys.md)。
    - 密钥保管库必须与用于数据的存储帐户位于同一区域。 可以将多个存储帐户与 Azure Data Box 资源链接。
    - 如果尚无密钥保管库，也可按照以下部分所述以内联方式创建它。

## <a name="enable-keys"></a>启用密钥

为 Azure Data Box 配置客户管理的密钥这一操作是可选的。 默认情况下，Data Box 使用 Microsoft 管理的密钥来保护 BitLocker 密钥。 若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 转到 Data Box 订单的“概述”边栏选项卡。

    ![Data Box 订单的“概述”边栏选项卡](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. 转到“设置”>“加密”。 在“加密类型”下，可以选择要如何保护设备解锁密钥。 默认情况下，使用 Microsoft 管理的密钥保护设备解锁密码。 

    ![选择加密选项](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. 选择“客户管理的密钥”作为加密类型。 选择客户管理的密钥后，单击“选择密钥保管库和密钥”。

    ![选择客户管理的密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. 在“从 Azure Key Vault 中选择密钥”边栏选项卡中，订阅会自动填充。 对于“密钥保管库”，可以从下拉列表中选择现有的密钥保管库。

    ![创建新的 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    还可以选择“新建”来创建新的密钥保管库。 在“创建密钥保管库”边栏选项卡中，输入资源组和密钥保管库名称。 确保已启用“软删除”和“清除保护”。 接受其他所有默认值。 选择“查看 + 创建”。

    ![创建新的 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. 查看与密钥保管库关联的信息，然后选择“创建”。 等待几分钟，以便完成密钥保管库的创建。

    ![创建 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. 在“从 Azure Key Vault 中选择密钥”中，可以选择现有密钥保管库中的密钥。

    ![在 Azure Key Vault 中创建新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. 如果要创建新密钥，请选择“新建”以创建密钥。 RSA 密钥大小可以是 2048 或更大。

    ![在 Azure Key Vault 中创建新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. 提供密钥的名称，接受其他默认值，然后选择“创建”。 

    ![创建新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. 系统会通知你已在密钥保管库中创建了密钥。 选择“版本”，然后选择“选择”。

    ![在密钥保管库中创建的新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. 在“加密类型”窗格中，可以看到为客户管理的密钥选择的密钥保管库和密钥。

    ![用于客户管理的密钥的密钥和密钥保管库](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. 保存密钥。 

    ![保存客户管理的密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    密钥 URL 在“加密类型”下显示。

    ![客户管理的密钥 URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> 在 Data Box 订单的任何阶段都可以禁用 Microsoft 管理的密钥，并改为使用客户管理的密钥。 但是，一旦创建客户管理的密钥，就无法切换回 Microsoft 管理的密钥。

## <a name="troubleshoot-errors"></a>排查错误

如果收到与客户管理的密钥相关的任何错误，请使用下表排除故障。

| 错误代码| 错误详细信息| 可恢复？|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| 无法提取密钥，因为客户管理的密钥已被禁用。| 是，通过启用密钥版本。|
| SsemUserErrorEncryptionKeyExpired| 无法提取密钥，因为客户管理的密钥已到期。| 是，通过启用密钥版本。|
| SsemUserErrorKeyDetailsNotFound| 无法提取密钥，因为无法找客户管理的密钥。| 如果删除了密钥保管库，则无法恢复客户管理的密钥。  如果将密钥保管库迁移到了其他租户，请参阅[订阅移动后更改密钥保管库租户 ID](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)。 如果删除了密钥保管库：<ol><li>是，如果它处于清除保护期内，请使用[恢复密钥保管库](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步骤。</li><li>否，如果超出了清除保护期。</li></ol><br>如果密钥保管库进行了租户迁移，则可以使用以下步骤之一进行恢复： <ol><li>将密钥保管库还原回旧租户。</li><li>设置 `Identity = None`，然后将值设置回 `Identity = SystemAssigned`。 这将在新标识创建后删除并重新创建该标识。 在密钥保管库的“访问策略”中为新标识启用 `Get`、`Wrap` 和 `Unwrap` 权限。</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| 无法提取密钥，因为对客户管理的密钥的访问权限已被撤销。| 是，检查以下事项： <ol><li>密钥保管库在访问策略中是否仍具有 MSI。</li><li>访问策略提供 Get、Wrap、Unwrap 权限。</li><li>如果密钥保管库位于防火墙后面的 VNet 中，请检查是否启用了“允许 Microsoft 信任的服务”。</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| 无法提取密钥，因为找不到与客户管理的密钥关联的密钥保管库。 | 如果删除了密钥保管库，则无法恢复客户管理的密钥。  如果将密钥保管库迁移到了其他租户，请参阅[订阅移动后更改密钥保管库租户 ID](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)。 如果删除了密钥保管库：<ol><li>是，如果它处于清除保护期内，请使用[恢复密钥保管库](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步骤。</li><li>否，如果超出了清除保护期。</li></ol><br>如果密钥保管库进行了租户迁移，则可以使用以下步骤之一进行恢复： <ol><li>将密钥保管库还原回旧租户。</li><li>设置 `Identity = None`，然后将值设置回 `Identity = SystemAssigned`。 这将在新标识创建后删除并重新创建该标识。 在密钥保管库的“访问策略”中为新标识启用 `Get`、`Wrap` 和 `Unwrap` 权限。</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | 无法提取密钥，因为无法找客户管理的密钥。| 是，检查以下事项： <ol><li>密钥保管库在访问策略中是否仍具有 MSI。</li><li>标识的类型为“系统分配”。</li><li>对密钥保管库的访问策略中的标识启用获取、包装和解包权限。</li></ol>|
| 常规错误  | 无法提取密钥。| 这是一般性错误。 请联系 Microsoft 支持部门以解决此错误并确定后续步骤。|


## <a name="next-steps"></a>后续步骤

- [什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)