---
title: 为静态 Azure 服务总线数据配置你自己的密钥
description: 本文提供了有关如何配置自己的密钥来加密 Azure 服务总线数据的信息。
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 356f825524192c3b6cf7df7f0460975f23ea4f7c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852284"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal-preview"></a>使用 Azure 门户（预览版）配置客户托管的密钥，以便在静态上加密 Azure 服务总线数据
Azure 服务总线高级版通过 Azure 存储服务加密（Azure SSE）提供静态数据的加密。 服务总线高级依赖于 Azure 存储空间来存储数据，默认情况下，使用 Microsoft 托管密钥对存储在 Azure 存储中的所有数据进行加密。 

## <a name="overview"></a>概述
Azure 服务总线现在支持通过 Microsoft 管理的密钥或客户托管的密钥（创建自己的密钥 BYOK）来加密静态数据。 此功能使你能够创建、轮换、禁用和撤消对用于加密 Azure 服务总线的客户托管密钥的访问权限。

启用 BYOK 功能只是在命名空间上进行一次设置。

> [!NOTE]
> 对于服务端加密的客户托管密钥，需要注意一些事项。 
>   * [Azure 服务总线高级](service-bus-premium-messaging.md)层支持此功能。 不能为标准层服务总线命名空间启用此功能。
>   * 只能为新的或空的命名空间启用加密。 如果命名空间包含数据，则加密操作将失败。
>   * 如果在服务总线命名空间 Azure Key Vault 上配置了[虚拟网络（VNet）服务终结点](service-bus-service-endpoints.md)，则将不支持 BYOK。 

你可以使用 Azure Key Vault 来管理密钥并审核密钥用法。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault Api 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/key-vault-overview.md)

本文介绍如何使用 Azure 门户配置包含客户管理密钥的密钥保管库。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户从 Azure Key Vault 设置和检索机密](../key-vault/quick-create-portal.md)。

> [!IMPORTANT]
> 若要将客户托管密钥用于 Azure 服务总线，要求密钥保管库配置了两个必需的属性。 它们是：**软删除**和不**清除**。 默认情况下，在 Azure 门户中创建新的密钥保管库时，将启用这些属性。 但是，如果需要在现有的密钥保管库上启用这些属性，则必须使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥
若要在 Azure 门户中启用客户托管的密钥，请执行以下步骤：

1. 导航到服务总线高级命名空间。
2. 在服务总线命名空间的 "**设置**" 页上，选择 "**加密（预览）** "。
3. 选择**客户托管的密钥加密**，如下图所示。

    ![启用客户托管的密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>使用密钥设置密钥保管库

启用客户管理的密钥后，需要将客户托管密钥与 Azure 服务总线命名空间相关联。 Service Bus 仅支持 Azure Key Vault。 如果在上一节中启用 "**使用客户管理的密钥加密**" 选项，则需要将密钥导入 Azure Key Vault。 此外，这些密钥必须包含**软删除**，并且**不会清除**密钥。 可以使用[PowerShell](../key-vault/key-vault-soft-delete-powershell.md)或[CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)配置这些设置。

1. 若要创建新的密钥保管库，请按照 Azure Key Vault[快速入门](../key-vault/key-vault-overview.md)中的步骤进行操作。 有关导入现有密钥的详细信息，请参阅[关于密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md)。
1. 若要在创建保管库时启用软删除和清除保护，请使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要将清除保护添加到现有保管库（已启用软删除），请使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 按照以下步骤创建密钥：
    1. 若要创建新密钥，请从“设置”下的“密钥”菜单中选择“生成/导入”。
        
        ![选择 "生成/导入" 按钮](./media/configure-customer-managed-key/select-generate-import.png)

    1. 将“选项”设置为“生成”并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 

    1. 你现在可以从下拉列表中选择此密钥以与要进行加密的服务总线命名空间相关联。 

        ![从 key vault 中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 为实现冗余，最多可以添加3个键。 如果其中一个密钥已过期或无法访问，则其他密钥将用于加密。
        
    1. 填写密钥的详细信息，并单击 "**选择**"。 这将允许使用客户管理的密钥加密命名空间上的静态数据。 


> [!IMPORTANT]
> 如果你想要使用客户托管密钥以及异地灾难恢复，请查看以下- 
>
> 若要使用客户托管密钥启用静态加密，请在指定的 Azure KeyVault 上为服务总线的托管标识设置[访问策略](../key-vault/key-vault-secure-your-key-vault.md)。 这可确保从 Azure 服务总线命名空间控制对 Azure KeyVault 的访问。
>
> 原因如下：
> 
>   * 如果已为服务总线命名空间启用[异地灾难恢复](service-bus-geo-dr.md)，并且你希望启用客户托管密钥，则 
>     * 断开配对
>     * 为密钥保管库的主命名空间和辅助命名空间设置托管标识的[访问策略](../key-vault/managed-identity.md)。
>     * 在主命名空间上设置加密。
>     * 重新配对主命名空间和辅助命名空间。
> 
>   * 如果你想要在已设置客户托管密钥的服务总线命名空间上启用异地灾难恢复，则-
>     * 为密钥保管库的辅助命名空间的托管标识[设置访问策略](../key-vault/managed-identity.md)。
>     * 配对主命名空间和辅助命名空间。


## <a name="rotate-your-encryption-keys"></a>旋转加密密钥

可以通过使用 Azure 密钥保管库旋转机制，在密钥保管库中轮换密钥。 有关详细信息，请参阅[设置密钥轮替和审核](../key-vault/key-vault-key-rotation-log-monitoring.md)。 激活和到期日期还可以设置为自动执行密钥轮换。 Service Bus 服务将检测新的密钥版本，并自动开始使用它们。

## <a name="revoke-access-to-keys"></a>撤消对密钥的访问权限

撤消对加密密钥的访问权限不会从服务总线中清除数据。 但是，无法从服务总线命名空间访问数据。 可以通过访问策略或通过删除密钥来撤消加密密钥。 了解有关访问策略的详细信息，以及如何保护密钥保管库免受[对密钥保管库的安全访问](../key-vault/key-vault-secure-your-key-vault.md)。

撤消加密密钥后，已加密命名空间上的服务总线服务将无法操作。 如果启用了对密钥的访问或还原了删除的密钥，则服务总线服务将选取密钥，以便可以从加密的服务总线命名空间访问数据。

> [!NOTE]
> 如果从密钥保管库中删除现有加密密钥，并将其替换为服务总线命名空间上的新密钥，因为删除密钥在多达一小时内仍有效（缓存），所以仍可以访问旧数据（使用旧密钥加密） alon使用新的数据进行 g 操作，现在只能使用新密钥进行访问。 此行为是在此功能的预览版本中设计的。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [服务总线概述](service-bus-messaging-overview.md)
- [Key Vault 概述](../key-vault/key-vault-overview.md)


