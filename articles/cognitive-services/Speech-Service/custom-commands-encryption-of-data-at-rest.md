---
title: 自定义命令服务静态数据的加密
titleSuffix: Azure Cognitive Services
description: 静态静态数据的自定义命令。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 83b6e6be8764a86c41bd9156cc96f8a594dbe1e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294023"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>对静态数据的自定义命令加密

自定义命令将数据保存到云时，会自动对其进行加密。 自定义命令服务加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

> [!NOTE]
> 自定义命令服务不会为与应用程序关联的 LUIS 资源自动启用加密。 如果需要，你必须从 [此处](./../LUIS/luis-encryption-of-data-at-rest.md)为 LUIS 资源启用加密。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密
使用符合 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [的256位 AES 加密对](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 数据进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

使用自定义命令时，speech service 会将以下数据存储在云中：
* 自定义命令应用程序背后的配置 JSON
* LUIS 创作和预测密钥

默认情况下，订阅使用 Microsoft 托管的加密密钥。 不过，你也可以使用自己的加密密钥来管理订阅。 客户管理的密钥 (CMK)（也称为创建自己的密钥，BYOK）在创建、轮换、禁用和撤销访问控制方面可提供更大的灵活性。 此外，你还可以审核用于保护数据的加密密钥。


> [!IMPORTANT]
> 客户托管的密钥只是在2020年6月27日以后创建的可用资源。 若要将 CMK 与语音服务一起使用，需要创建新的语音资源。 创建资源后，可以使用 Azure Key Vault 来设置托管标识。

若要请求使用客户管理的密钥的功能，请填写并提交 Customer-Managed 密钥请求表单。 大约需要3-5 个工作日内就会收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准使用 CMK 与语音服务后，需要从 Azure 门户创建新的语音资源。
   > [!NOTE]
   > **只有自定义命令支持客户管理的密钥 (CMK) 。**
   >
   >  **自定义语音和自定义语音仍然支持仅将自己的存储 (BYOS) 。**  [了解详细信息](speech-encryption-of-data-at-rest.md)
   >
   > 如果你使用给定的语音资源来访问这些服务，则必须通过显式配置 BYOS 满足符合性要求。


## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥和 Azure Key Vault

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 语音资源和密钥保管库必须位于同一区域，并且在相同的 Azure Active Directory (Azure AD) 租户中，但它们可以位于不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

当创建新的语音资源并用于设置自定义命令时，应用程序数据始终使用 Microsoft 托管的密钥进行加密。 创建资源时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，此访问策略向与认知服务资源关联的托管标识授予密钥权限。 仅在使用 CMK 所需的定价层创建资源后，托管标识才可用。

启用客户托管的密钥还将启用系统分配的 [托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)，一项功能 Azure AD。 启用系统分配的托管标识后，此资源将注册 Azure Active Directory。 注册后，将向托管标识授予对客户管理密钥安装过程中选择的 Key Vault 的访问权限。 

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则将删除对密钥保管库的访问权限，并且任何使用客户密钥加密的数据将无法再访问。 所有依赖于此数据的功能都将停止工作。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，会自动在这些内容下分配托管标识。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个目录，则与该资源关联的托管标识不会传输到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”。  

## <a name="configure-azure-key-vault"></a>配置 Azure Key Vault

使用客户托管的密钥需要在密钥保管库中设置两个属性， **软删除** 并不 **清除**。 默认不会启用这些属性，但可以使用 PowerShell 或 Azure CLI 对新的或现有的 Key Vault 启用。

> [!IMPORTANT]
> 如果你没有 " **软删除** " 和 " **未** 启用启用属性" 并删除你的密钥，则无法恢复认知服务资源中的数据。

若要了解如何在现有密钥保管库上启用这些属性，请参阅以下文章之一中标题为“启用软删除”和“启用清除保护”的部分： 

- [如何通过 PowerShell 使用软删除](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)。
- [如何通过 CLI 使用软删除](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)。

Azure 存储加密仅支持大小为 2048 的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的“Key Vault 密钥”。

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>为语音资源启用客户管理的密钥

若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到语音资源。
1. 在语音资源的 " **设置** " 边栏选项卡上，单击 " **加密**"。 选择 " **客户托管密钥** " 选项，如下图所示。

 ![显示如何选择客户托管密钥的屏幕截图](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>指定密钥

启用客户管理的密钥后，你将有机会指定与认知服务资源关联的密钥。

### <a name="specify-a-key-as-a-uri"></a>将密钥指定为 URI

若要将某个密钥指定为 URI，请执行下列步骤：

1. 若要在 Azure 门户中查找密钥 URI，请导航到 Key Vault，然后选择“密钥”设置。 选择所需的密钥，然后单击该密钥以查看其版本。 选择一个密钥版本，查看该版本的设置。
1. 复制“密钥标识符”字段的值（提供 URI）。

    ![显示 Key Vault 密钥 URI 的屏幕截图](../media/cognitive-services-encryption/key-uri-portal.png)

1. 在语音服务的 " **加密** 设置" 中，选择 " **输入密钥 URI** " 选项。
1. 将复制的 URI 粘贴到“密钥 URI”字段中。

1. 指定包含密钥保管库的订阅。
1. 保存所做更改。

### <a name="specify-a-key-from-a-key-vault"></a>从 Key Vault 指定密钥

若要指定 Key Vault 中的密钥，请先请确保有一个包含密钥的 Key Vault。 若要指定 Key Vault 中的密钥，请执行以下步骤：

1. 选择“从 Key Vault 中选择”选项。
1. 选择包含要使用的密钥的密钥保管库。
1. 从密钥保管库中选择密钥。

   ![显示客户管理的密钥选项的屏幕截图](media/custom-commands/configure-cmk-fromKV.png)

1. 保存所做更改。

## <a name="update-the-key-version"></a>更新密钥版本

当你创建密钥的新版本时，请更新语音资源以使用新版本。 执行以下步骤:

1. 导航到语音资源并显示 **加密** 设置。
1. 输入新密钥版本的 URI。 或者，可以再次选择 Key Vault 和密钥以更新版本。
1. 保存所做更改。

## <a name="use-a-different-key"></a>使用其他密钥

若要更改用于加密的密钥，请执行以下步骤：

1. 导航到语音资源并显示 **加密** 设置。
1. 输入新密钥的 URI。 也可选择密钥保管库并选择一个新密钥。
1. 保存所做更改。

## <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 旋转密钥时，必须更新语音资源以使用新的密钥 URI。 若要了解如何更新资源以在 Azure 门户中使用新版本的密钥，请参阅 [更新密钥版本](#update-the-key-version)。

旋转密钥不会触发重新加密资源中的数据。 用户无需执行任何其他操作。

## <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 或 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤消访问权限会有效地阻止访问认知服务资源中的所有数据，因为认知服务无法访问加密密钥。

## <a name="disable-customer-managed-keys"></a>禁用客户托管密钥

当你禁用客户管理的密钥时，你的语音资源将由 Microsoft 管理的密钥加密。 若要禁用客户托管密钥，请执行以下步骤：

1. 导航到语音资源并显示 **加密** 设置。
1. 取消选中“使用自己的密钥”设置旁边的复选框。

## <a name="next-steps"></a>后续步骤

* [Speech Customer-Managed 密钥请求窗体](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [什么是托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)



