---
title: 语言理解静态数据的服务加密
titleSuffix: Azure Cognitive Services
description: 语言理解静态数据的服务加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372333"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>语言理解静态数据的服务加密

语言理解服务在将数据保存到云时，会自动对其进行加密。 语言理解服务加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用符合[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [的256位 AES 加密对](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)数据进行加密和解密。 加密和解密是透明的，这意味着对加密和访问进行管理。 默认情况下，你的数据是安全的，无需修改你的代码或应用程序即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 还可以选择使用自己的密钥来管理订阅。 客户托管的密钥（CMK）提供了更大的灵活性，可用于创建、轮换、禁用和撤消访问控制。 你还可以审核用于保护数据的加密密钥。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户托管的密钥与 Azure Key Vault

还可以选择使用自己的密钥来管理订阅。 客户托管的密钥（CMK）（也称为自带密钥（BYOK））提供了更大的灵活性，可用于创建、轮换、禁用和撤销访问控制。 你还可以审核用于保护数据的加密密钥。

必须使用 Azure Key Vault 来存储客户管理的密钥。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault Api 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域和相同的 Azure Active Directory （Azure AD）租户中，但它们可以位于不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

### <a name="customer-managed-keys-for-language-understanding"></a>语言理解的客户托管密钥

若要请求使用客户管理的密钥的功能，请填写并提交 [LUIS 服务客户托管的密钥请求表单](https://aka.ms/cogsvc-cmk)。 大约需要3-5 个工作日内就会收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准将 CMK 与 LUIS 配合使用后，需要从 Azure 门户创建新的语言理解资源，并选择 E0 作为定价层。 新 SKU 的工作方式与已提供的 F0 SKU 相同（CMK 除外）。 用户无法从 F0 升级到新的 E0 SKU。

E0 资源仅可用于创作服务，并且仅在美国西部地区才支持 E0 层。

![LUIS 订阅图像](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>区域可用性

客户托管的密钥目前在**美国西部**区域中可用。

### <a name="limitations"></a>限制

使用带有现有/以前创建的应用程序的 E0 层时有一些限制：

* 迁移到 E0 资源将被阻止。 用户只能将其应用迁移到 F0 资源。 将现有资源迁移到 F0 后，可以在 E0 层中创建新的资源。 有关迁移的详细信息，请参阅[此处](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)。  
* 阻止将应用程序移到 E0 资源或从 E0 资源移动应用程序。 此限制的解决方法是导出现有的应用程序，并将其作为 E0 资源导入。
* 不支持必应拼写检查功能。
* 如果你的应用程序是 E0，则会禁用日志记录最终用户流量。
* 对于 E0 层中的应用程序，Azure Bot 服务中的语音填充功能不受支持。 此功能通过 Azure Bot 服务提供，该服务不支持 CMK。
* 门户中的语音填充功能需要 Azure Blob 存储。 有关详细信息，请参阅[自带存储](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)。

### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

新的认知服务资源始终使用 Microsoft 托管的密钥进行加密。 创建资源时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，此访问策略向与认知服务资源关联的托管标识授予密钥权限。 只有使用 CMK 定价层创建了资源后，托管标识才可用。

若要了解如何将客户托管的密钥用于认知服务加密的 Azure Key Vault，请参阅：

- [将客户托管的密钥配置 Key Vault 用于认知服务加密的 Azure 门户](../Encryption/cognitive-services-encryption-keys-portal.md)

启用客户托管的密钥还将启用系统分配的托管标识，一项功能 Azure AD。 启用系统分配的托管标识后，此资源将注册 Azure Active Directory。 注册后，将向托管标识授予对客户管理密钥安装过程中选择的 Key Vault 的访问权限。 你可以了解有关[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的详细信息。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则将删除对密钥保管库的访问权限，并且任何使用客户密钥加密的数据将无法再访问。 所有依赖于此数据的功能都将停止工作。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，会自动在这些内容下分配托管标识。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个目录，则与该资源关联的托管标识不会传输到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅在常见问题中**传输 Azure AD 目录之间的订阅**[和 Azure 资源的托管标识的已知问题](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户托管的密钥存储在 Azure Key Vault

若要启用客户管理的密钥，必须使用 Azure Key Vault 来存储密钥。 必须同时启用**软删除**和不**清除**密钥保管库中的属性。

认知服务加密仅支持大小为2048的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**Key Vault 密钥**。

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

你可以根据你的符合性策略，在 Azure Key Vault 中轮替客户管理的密钥。 当密钥旋转时，必须更新认知服务资源才能使用新密钥 URI。 若要了解如何更新资源以在 Azure 门户中使用新版本的密钥，请参阅[使用 Azure 门户为认知服务配置客户管理的密钥](../Encryption/cognitive-services-encryption-keys-portal.md)中的 "**更新密钥版本**" 一节。

旋转密钥不会触发重新加密资源中的数据。 用户无需执行任何其他操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//)或[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤消访问权限会有效地阻止访问认知服务资源中的所有数据，因为认知服务无法访问加密密钥。

## <a name="next-steps"></a>后续步骤

* [LUIS 服务客户托管的密钥请求窗体](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
