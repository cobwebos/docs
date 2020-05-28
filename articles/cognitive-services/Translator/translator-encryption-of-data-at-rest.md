---
title: 静态数据的转换器加密
titleSuffix: Azure Cognitive Services
description: 静态数据加密的转换器。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: egeaney
ms.openlocfilehash: 0a4a1f49735a30d7ad764e7f031610eabd09763e
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995762"
---
# <a name="translator-encryption-of-data-at-rest"></a>静态数据的转换器加密

在将数据保存到云时，转换器会自动对你上载的数据进行加密，以构建自定义翻译模型，有助于满足组织的安全性和符合性目标。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用符合[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [的256位 AES 加密对](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)数据进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 如果你使用的定价层支持客户管理的密钥，则可以在[Azure 门户](https://portal.azure.com)的 "**加密**" 部分中看到资源的加密设置，如下图所示。

![查看加密设置](../media/cognitive-services-encryption/encryptionblade.png)

对于仅支持 Microsoft 托管的加密密钥的订阅，你将没有**加密**部分。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥和 Azure Key Vault

还可以选择使用自己的密钥来管理订阅。 客户托管的密钥（CMK）（也称为自带密钥（BYOK））提供了更大的灵活性，可用于创建、轮换、禁用和撤销访问控制。 还可以审核用于保护数据的加密密钥。

> [!IMPORTANT]
> 客户管理的密钥适用于转换器服务的所有定价层。 若要请求使用客户管理的密钥的功能，请填写并提交[翻译人员客户管理的密钥请求窗体](https://aka.ms/cogsvc-cmk)，大约需要3-5 个工作日内，才能收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准使用 CMK 与 Translator 服务后，你将需要创建一个新的转换器资源。 创建转换器资源后，可以使用 Azure Key Vault 来设置托管标识。

请按照以下步骤为转换器启用客户管理的密钥：

1. 创建新的区域翻译员或区域认知服务资源。 这不适用于全局资源。
2. 已在 Azure 门户中启用托管标识，并添加客户托管的密钥信息。
3. 在自定义转换器中创建新的工作区，并将此订阅信息关联起来。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域和相同的 Azure Active Directory （Azure AD）租户中，但它们可以位于不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

新的认知服务资源始终使用 Microsoft 托管的密钥进行加密。 创建资源时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，此访问策略向与认知服务资源关联的托管标识授予密钥权限。 一旦创建了资源，托管标识就会立即可用。

若要了解如何将客户托管的密钥用于认知服务加密的 Azure Key Vault，请参阅：

- [将客户托管的密钥配置 Key Vault 用于认知服务加密的 Azure 门户](../Encryption/cognitive-services-encryption-keys-portal.md)

启用客户托管的密钥还将启用系统分配的托管标识，一项功能 Azure AD。 启用系统分配的托管标识后，此资源将注册 Azure Active Directory。 注册后，将向托管标识授予对客户管理密钥安装过程中选择的 Key Vault 的访问权限。 你可以了解有关[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的详细信息。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则将删除对密钥保管库的访问权限，并且任何使用客户密钥加密的数据将无法再访问。 所有依赖于此数据的功能都将停止工作。 还将取消部署已部署的任何模型。 所有上传的数据都将从自定义转换程序中删除。 如果重新启用了托管标识，则不会自动为您重新部署模型。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，会自动在这些内容下分配托管标识。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个目录，则与该资源关联的托管标识不会传输到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”****。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户管理的密钥存储在 Azure 密钥保管库

若要启用客户管理的密钥，必须使用 Azure Key Vault 来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性********。

认知服务加密仅支持大小为2048的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的“Key Vault 密钥”。****

> [!NOTE]
> 如果删除整个密钥保管库，将不再显示你的数据，并且将取消部署所有模型。 所有上传的数据都将从自定义转换程序中删除。 

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 或 [Azure 密钥保管库 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤消访问权限会有效地阻止访问认知服务资源中的所有数据，并且将取消部署您的模型，因为认知服务无法访问加密密钥。 所有上传的数据也将从自定义转换程序中删除。


## <a name="next-steps"></a>后续步骤

* [详细了解 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
