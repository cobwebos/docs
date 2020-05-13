---
title: 使用 Azure 门户为 Azure Data Box 配置客户托管的密钥
description: 了解如何使用 Azure 门户为 Azure Data Box 的 Azure Key Vault 配置客户管理的密钥。 使用客户管理的密钥，你可以创建、轮换、禁用和撤销访问控制。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 6f543b3f5c2bb7d4949c431580771c4b0d965e4d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125284"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>在 Azure Key Vault 中使用客户托管的密钥 Azure Data Box

Azure Data Box 通过加密密钥来保护用于锁定设备的设备解锁密钥（也称为设备密码）。 默认情况下，使用 Microsoft 托管密钥对 Data Box 订单的设备解锁密钥进行加密。 若要进一步控制设备解锁密钥，还可以提供客户管理的密钥。 

必须创建客户管理的密钥并将其存储在 Azure Key Vault 中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../key-vault/general/overview.md)。

本文介绍如何在[Azure 门户](https://portal.azure.com/)中使用客户管理的密钥与 Azure Data Box。 本文适用于 Azure Data Box 设备和 Azure Data Box Heavy 设备。

## <a name="prerequisites"></a>必备条件

在开始之前，请确保：

1. 根据[教程： order Azure Data Box](data-box-deploy-ordered.md)中的说明创建了 Azure Data Box 顺序。

2. 你有一个现有的 Azure Key Vault，其中包含一个可用于保护设备解锁密钥的密钥。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户从 Azure Key Vault 设置和检索机密](../key-vault/secrets/quick-create-portal.md)。

    - 在现有的密钥保管库上设置**软删除**和不**清除**。 默认情况下未启用这些属性。 若要启用这些属性，请参阅以下文章之一中标题为**启用软删除**和**启用清除保护**的部分：

        - [如何将软删除与 PowerShell 配合使用](../key-vault/general/soft-delete-powershell.md)。
        - [如何通过 CLI 使用软删除](../key-vault/general/soft-delete-cli.md)。
    - 现有密钥保管库的 RSA 密钥应为2048或更大。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥](../key-vault/keys/about-keys.md)。
    - Key vault 必须与用于数据的存储帐户位于同一区域。 可以将多个存储帐户与 Azure Data Box 资源链接在一起。
    - 如果没有现有的密钥保管库，还可以按照以下部分中所述，以内联方式创建密钥保管库。

## <a name="enable-keys"></a>启用密钥

为 Azure Data Box 配置客户托管的密钥是可选的。 默认情况下，Data Box 使用 Microsoft 托管密钥保护你的 BitLocker 密钥。 若要在 Azure 门户中启用客户托管的密钥，请执行以下步骤：

1. 请在 "**概述**" 边栏选项卡中查看 Data Box 订单。

    ![Data Box 订单的概览边栏选项卡](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. **> 加密**"中转到" 设置 "。 在 "**加密类型**" 下，你可以选择要如何保护设备解锁密钥。 默认情况下，Microsoft 托管密钥用于保护设备解锁密码。 

    ![选择加密选项](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. 选择 "加密类型" 作为 "**客户托管密钥**"。 选择了客户管理的密钥后，请**选择密钥保管库和密钥**。

    ![选择客户托管的密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. 在**从 Azure Key Vault**边栏选项卡中选择 "密钥"，会自动填充订阅。 对于**密钥保管库**，可以从下拉列表中选择现有的密钥保管库。

    ![新建 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    你还可以选择 "**新建**" 以创建新的密钥保管库。 在 "**创建密钥保管库" 边栏选项卡**中，输入资源组和密钥保管库名称。 确保已启用**软删除**和**清除保护**。 接受所有其他默认值。 选择“查看 + 创建”  。

    ![新建 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. 查看与密钥保管库关联的信息，然后选择 "**创建**"。 等待几分钟，以便完成密钥保管库的创建。

    ![创建 Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. 在 Azure Key Vault 的 "**选择密钥**" 中，可以选择现有密钥保管库中的密钥。

    ![在 Azure Key Vault 中创建新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. 如果要创建新密钥，请选择 "**新建**" 以创建一个密钥。 RSA 密钥大小可以是2048或更高。

    ![在 Azure Key Vault 中创建新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. 提供密钥的名称，接受其他默认值，然后选择 "**创建**"。 

    ![创建新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. 系统会通知你已在 key vault 中创建密钥。 选择**版本**，然后选择 "**选择**"。

    ![在密钥保管库中创建的新密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. 在 "**加密类型**" 窗格中，可以看到密钥保管库以及为客户托管的密钥选择的密钥。

    ![客户托管密钥的密钥和密钥保管库](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. 保存密钥。 

    ![保存客户托管的密钥](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    密钥 URL 以 "**加密类型**" 显示。

    ![客户托管的密钥 URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> 可以禁用 Microsoft 托管密钥，并在 Data Box 订单的任意阶段移动到客户托管的密钥。 但是，一旦创建了客户管理的密钥，就无法切换回 Microsoft 托管密钥。

## <a name="troubleshoot-errors"></a>排查错误

如果收到与客户托管密钥相关的任何错误，请使用下表进行故障排除。

| 错误代码| 错误消息| 详细信息|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| 无法提取密钥，因为已禁用 customer 托管密钥。| 是，通过启用密钥版本。|
| SsemUserErrorEncryptionKeyExpired| 无法提取密钥，因为客户托管密钥已过期。| 是，通过启用密钥版本。|
| SsemUserErrorKeyDetailsNotFound| 无法提取密钥，因为找不到客户托管密钥。| 如果删除了密钥保管库，则无法恢复客户管理的密钥。  如果已将密钥保管库迁移到其他租户，请参阅在[订阅移动后更改密钥保管库租户 ID](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)。 如果已删除密钥保管库：<ol><li>如果是，则为 "是"，如果它处于 "清除保护" 持续时间，请使用[恢复密钥保管库](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步骤。</li><li>否，如果超出了清除保护持续时间。</li></ol><br>如果密钥保管库耗费租户迁移，是的，可以使用以下步骤之一恢复： <ol><li>将密钥保管库还原回旧租户。</li><li>设置 `Identity = None` ，然后将值设置回 `Identity = SystemAssigned` 。 创建新标识后，此操作将删除并重新创建标识。 `Get` `Wrap` `Unwrap` 对密钥保管库的访问策略中的新标识启用、和权限。</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| 无法提取密钥，因为已吊销客户托管密钥访问权限。| 是，检查： <ol><li>密钥保管库在访问策略中仍具有 MSI。</li><li>访问策略提供获取、包装、解包的权限。</li><li>如果密钥保管库位于防火墙后面的 vNet 中，请选中 "**允许 Microsoft 可信服务**已启用"。</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| 无法提取密钥，因为找不到客户托管密钥的关联密钥保管库。 | 如果删除了密钥保管库，则无法恢复客户管理的密钥。  如果已将密钥保管库迁移到其他租户，请参阅在[订阅移动后更改密钥保管库租户 ID](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix)。 如果已删除密钥保管库：<ol><li>如果是，则为 "是"，如果它处于 "清除保护" 持续时间，请使用[恢复密钥保管库](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)中的步骤。</li><li>否，如果超出了清除保护持续时间。</li></ol><br>如果密钥保管库耗费租户迁移，是的，可以使用以下步骤之一恢复： <ol><li>将密钥保管库还原回旧租户。</li><li>设置 `Identity = None` ，然后将值设置回 `Identity = SystemAssigned` 。 创建新标识后，此操作将删除并重新创建标识。 `Get` `Wrap` `Unwrap` 对密钥保管库的访问策略中的新标识启用、和权限。</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | 无法提取密钥，因为找不到客户托管密钥。| 是，检查： <ol><li>密钥保管库在访问策略中仍具有 MSI。</li><li>标识的类型为系统分配的类型。</li><li>对密钥保管库的访问策略中的标识启用获取、包装和解包权限。</li></ol>|
| 常规错误  | 无法提取密钥。| 这是一个一般性错误。 请联系 Microsoft 支持部门以解决此错误并确定后续步骤。|


## <a name="next-steps"></a>后续步骤

- [什么是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？