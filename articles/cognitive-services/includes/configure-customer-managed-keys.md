---
title: 使用 Azure 门户配置客户管理的密钥
titleSuffix: Cognitive Services
description: 了解如何使用 Azure 门户配置 Azure Key Vault 客户管理的密钥。 使用客户管理的密钥，你可以创建、轮换、禁用和撤销访问控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 63cfe7968ec88ed75dfe23e8a3d34ac2649f6776
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307792"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥和 Azure Key Vault

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure 密钥保管库 API 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域和相同的 Azure Active Directory （Azure AD）租户中，但它们可以位于不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

创建新的认知服务资源时，将始终使用 Microsoft 托管密钥对其进行加密。 创建资源时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，此访问策略向与认知服务资源关联的托管标识授予密钥权限。 仅在使用 CMK 所需的定价层创建资源后，托管标识才可用。

启用客户托管的密钥还将启用系统分配的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)，一项功能 Azure AD。 启用系统分配的托管标识后，此资源将注册 Azure Active Directory。 注册后，将向托管标识授予对客户管理密钥安装过程中选择的 Key Vault 的访问权限。 

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则将删除对密钥保管库的访问权限，并且任何使用客户密钥加密的数据将无法再访问。 所有依赖于此数据的功能都将停止工作。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，会自动在这些内容下分配托管标识。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个目录，则与该资源关联的托管标识不会传输到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”****。  

## <a name="configure-azure-key-vault"></a>配置 Azure Key Vault

使用客户托管的密钥需要在密钥保管库中设置两个属性，**软删除**并不**清除**。 默认不会启用这些属性，但可以使用 PowerShell 或 Azure CLI 对新的或现有的 Key Vault 启用。

> [!IMPORTANT]
> 如果你没有 "**软删除**" 和 "**未**启用启用属性" 并删除你的密钥，则无法恢复认知服务资源中的数据。

若要了解如何在现有的密钥保管库上启用这些属性，请参阅下方其中一篇文章中标题为“启用软删除”和“启用清除保护”的部分 ：

- [如何将软删除与 PowerShell 配合使用](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)。
- [如何将软删除与 CLI 配合使用](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)。

Azure 存储加密仅支持大小为 2048 的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的“Key Vault 密钥”。****

## <a name="enable-customer-managed-keys-for-your-resource"></a>为资源启用客户管理的密钥

若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到认知服务资源。
1. 在认知服务资源的 "**设置**" 边栏选项卡上，单击 "**加密**"。 选择 "**客户托管密钥**" 选项，如下图所示。

    ![显示如何选择客户托管密钥的屏幕截图](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>指定密钥

启用客户管理的密钥后，你将有机会指定与认知服务资源关联的密钥。

### <a name="specify-a-key-as-a-uri"></a>将密钥指定为 URI

若要将某个密钥指定为 URI，请执行下列步骤：

1. 若要在 Azure 门户中查找密钥 URI，请导航到 Key Vault，然后选择“密钥”设置。**** 选择所需的密钥，然后单击该密钥以查看其版本。 选择一个密钥版本，查看该版本的设置。
1. 复制提供 URI 的 "**密钥标识符**" 字段的值。

    ![显示 Key Vault 密钥 URI 的屏幕截图](../media/cognitive-services-encryption/key-uri-portal.png)

1. 在存储帐户的“加密”设置中，选择“输入密钥 URI”选项。********
1. 将复制的 URI 粘贴到“密钥 URI”**** 字段中。

   ![显示如何输入密钥 URI 的屏幕截图](../media/cognitive-services-encryption/ssecmk2.png)

1. 指定包含密钥保管库的订阅。
1. 保存所做更改。

### <a name="specify-a-key-from-a-key-vault"></a>从 Key Vault 指定密钥

若要指定 Key Vault 中的密钥，请先请确保有一个包含密钥的 Key Vault。 若要指定 Key Vault 中的密钥，请执行以下步骤：

1. 选择“从 Key Vault 中选择”**** 选项。
1. 选择包含要使用的密钥的密钥保管库。
1. 从密钥保管库中选择密钥。

   ![显示客户管理的密钥选项的屏幕截图](../media/cognitive-services-encryption/ssecmk3.png)

1. 保存所做更改。

## <a name="update-the-key-version"></a>更新密钥版本

当你创建新的密钥版本时，请更新认知服务资源以使用新版本。 执行以下步骤：

1. 导航到认知服务资源并显示**加密**设置。
1. 输入新密钥版本的 URI。 或者，可以再次选择 Key Vault 和密钥以更新版本。
1. 保存所做更改。

## <a name="use-a-different-key"></a>使用其他密钥

若要更改用于加密的密钥，请执行以下步骤：

1. 导航到认知服务资源并显示**加密**设置。
1. 输入新密钥的 URI。 也可选择密钥保管库并选择一个新密钥。
1. 保存所做更改。

## <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 当密钥旋转时，必须更新认知服务资源才能使用新密钥 URI。 若要了解如何更新资源以在 Azure 门户中使用新版本的密钥，请参阅[更新密钥版本](#update-the-key-version)。

旋转密钥不会触发重新加密资源中的数据。 用户无需执行任何其他操作。

## <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 或 [Azure 密钥保管库 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤消访问权限会有效地阻止访问认知服务资源中的所有数据，因为认知服务无法访问加密密钥。

## <a name="disable-customer-managed-keys"></a>禁用客户托管密钥

当你禁用客户管理的密钥时，你的认知服务资源将通过 Microsoft 托管的密钥进行加密。 若要禁用客户托管密钥，请执行以下步骤：

1. 导航到认知服务资源并显示**加密**设置。
1. 取消选中“使用自己的密钥”设置旁边的复选框。****