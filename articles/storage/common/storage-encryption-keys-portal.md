---
title: 使用 Azure 门户配置客户管理的密钥
titleSuffix: Azure Storage
description: 了解如何使用 Azure 门户通过 Azure Key Vault 来配置客户管理的密钥用于 Azure 存储加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a216714939dc45fd1b220f24414a527969ab7fcb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029550"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>通过 Azure 门户使用 Azure Key Vault 配置客户管理的密钥

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文介绍如何使用 [Azure 门户](https://portal.azure.com/)配置包含客户管理的密钥的 Azure 密钥保管库。 若要了解如何使用 Azure 门户创建 Key Vault，请参阅[快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索机密](../../key-vault/secrets/quick-create-portal.md)。

## <a name="configure-azure-key-vault"></a>配置 Azure Key Vault

使用带有 Azure 存储加密的客户管理密钥需要在密钥保管库上设置两个属性：“软删除”和“不要清除”。 默认不会启用这些属性，但可以使用 PowerShell 或 Azure CLI 对新的或现有的 Key Vault 启用。

若要了解如何在现有密钥保管库上启用这些属性，请参阅以下文章之一中标题为“启用软删除”和“启用清除保护”的部分： 

- [如何通过 PowerShell 使用软删除](../../key-vault/general/soft-delete-powershell.md)。
- [如何通过 CLI 使用软删除](../../key-vault/general/soft-delete-cli.md)。

Azure 存储加密支持2048、3072和4096大小的 RSA 和 RSA-HSM 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)中的“Key Vault 密钥”。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到存储帐户。
1. 在存储帐户的“设置”边栏选项卡上，单击“加密”。 选择“客户托管密钥”选项，如下图所示。

    ![显示加密选项的门户屏幕截图](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>指定密钥

启用客户管理的密钥后，可以指定要与存储帐户关联的密钥。 你还可以指示 Azure 存储应自动轮替客户托管的密钥，还是手动轮换密钥。

### <a name="specify-a-key-from-a-key-vault"></a>从 Key Vault 指定密钥

从密钥保管库中选择客户管理的密钥时，会自动启用密钥自动轮替。 若要手动管理密钥版本，请改为指定密钥 URI，并包含密钥版本。 有关详细信息，请参阅将[密钥指定为 URI](#specify-a-key-as-a-uri)。

若要指定 Key Vault 中的密钥，请执行以下步骤：

1. 选择“从 Key Vault 中选择”选项。
1. 选择 "**选择密钥保管库和密钥**"。
1. 选择包含要使用的密钥的密钥保管库。
1. 从密钥保管库中选择密钥。

   ![显示如何选择 key vault 和密钥的屏幕截图](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. 保存所做更改。

### <a name="specify-a-key-as-a-uri"></a>将密钥指定为 URI

指定密钥 URI 时，请省略密钥版本以启用客户托管密钥的自动轮替。 如果在密钥 URI 中包括密钥版本，则不启用自动轮替，并且你必须自行管理密钥版本。 有关更新密钥版本的详细信息，请参阅[手动更新密钥版本](#manually-update-the-key-version)。

若要将某个密钥指定为 URI，请执行下列步骤：

1. 若要在 Azure 门户中查找密钥 URI，请导航到 Key Vault，然后选择“密钥”设置。 选择所需的密钥，然后单击该密钥以查看其版本。 选择一个密钥版本，查看该版本的设置。
1. 复制“密钥标识符”字段的值（提供 URI）。

    ![显示 Key Vault 密钥 URI 的屏幕截图](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. 在存储帐户的 "**加密密钥**" 设置中，选择 "**输入密钥 URI** " 选项。
1. 将复制的 URI 粘贴到“密钥 URI”字段中。 从 URI 中省略密钥版本以启用自动旋转。

   ![显示如何输入密钥 URI 的屏幕截图](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. 指定包含密钥保管库的订阅。
1. 保存所做更改。

指定密钥后，Azure 门户指示是否启用了自动密钥轮替，并显示当前用于加密的密钥版本。

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="显示已启用客户托管密钥的自动轮替的屏幕截图":::

## <a name="manually-update-the-key-version"></a>手动更新密钥版本

默认情况下，Azure 存储会自动为你旋转客户管理的密钥，如前面几节中所述。 如果选择自行管理密钥版本，则每次创建密钥的新版本时，都必须更新为存储帐户指定的密钥版本。

若要将存储帐户更新为使用新的密钥版本，请执行以下步骤：

1. 导航到你的存储帐户，并显示“加密”设置。
1. 输入新密钥版本的 URI。 或者，可以再次选择 Key Vault 和密钥以更新版本。
1. 保存所做更改。

## <a name="switch-to-a-different-key"></a>切换到不同的密钥

若要更改用于 Azure 存储加密的密钥，请执行以下步骤：

1. 导航到你的存储帐户，并显示“加密”设置。
1. 输入新密钥的 URI。 也可选择密钥保管库并选择一个新密钥。
1. 保存所做更改。

## <a name="disable-customer-managed-keys"></a>禁用客户托管密钥

禁用客户托管密钥时，将再次使用 Microsoft 托管密钥对存储帐户进行加密。 若要禁用客户托管密钥，请执行以下步骤：

1. 导航到你的存储帐户，并显示“加密”设置。
1. 取消选中“使用自己的密钥”设置旁边的复选框。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
