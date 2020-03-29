---
title: 静态数据的转换器加密
titleSuffix: Azure Cognitive Services
description: 静态数据的转换器加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372234"
---
# <a name="translator-encryption-of-data-at-rest"></a>静态数据的转换器加密

当数据保存到云中时，转换器会自动加密数据，当您上传这些数据以构建自定义翻译模型时，有助于实现组织安全性和合规性目标。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)兼容[的 256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密对数据进行加密和解密。 加密和解密是透明的，这意味着加密和访问是为您管理的。 默认情况下，您的数据是安全的，您无需修改代码或应用程序就可以利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，您的订阅使用 Microsoft 管理的加密密钥。 如果使用的是支持客户管理的密钥的定价层，则可以在[Azure 门户](https://portal.azure.com)的 **"加密"** 部分中查看资源的加密设置，如下图所示。

![查看加密设置](../media/cognitive-services-encryption/encryptionblade.png)

对于仅支持 Microsoft 管理的加密密钥的订阅，您将没有**加密**部分。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥与 Azure Key Vault

还有一个选项可以用自己的密钥管理订阅。 客户管理的密钥 （CMK）也称为"自带密钥 （BYOK"），为创建、旋转、禁用和撤销访问控制提供了更大的灵活性。 还可以审核用于保护数据的加密密钥。

> [!IMPORTANT]
> 客户管理的密钥可用于翻译服务的所有定价层。 要请求使用客户管理的密钥，请填写并提交[翻译客户管理密钥请求表](https://aka.ms/cogsvc-cmk)，大约需要 3-5 个工作日才能回听您的请求状态。 根据需求，您可能会被放置在队列中，并在可用空间时获得批准。 一旦批准将 CMK 与翻译服务一起使用，您将需要创建新的翻译资源。 创建翻译资源后，可以使用 Azure 密钥保管库设置托管标识。

按照以下步骤为译员启用客户管理的密钥：

1. 创建新的区域翻译文本或区域认知服务资源。 这不适用于全局资源。
2. 在 Azure 门户中启用托管标识，并添加客户管理的关键信息。
3. 在自定义转换器中创建新工作区并关联此订阅信息。

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域和同一 Azure 活动目录 （Azure AD） 租户中，但它们可以位于不同的订阅中。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

新的认知服务资源始终使用 Microsoft 管理的密钥进行加密。 在创建资源时无法启用客户管理的密钥。 客户管理的密钥存储在 Azure 密钥保管库中，并且必须预配密钥保管库，这些访问策略向与认知服务资源关联的托管标识授予密钥权限。 创建资源后，托管标识即可用。

要了解如何将客户管理的密钥与 Azure 密钥保管库用于认知服务加密，请参阅：

- [使用密钥保管库配置客户管理的密钥，以便从 Azure 门户进行认知服务加密](../Encryption/cognitive-services-encryption-keys-portal.md)

启用客户托管密钥还将启用系统分配的托管标识，这是 Azure AD 的一项功能。 启用系统分配的托管标识后，此资源将注册到 Azure 活动目录。 注册后，托管标识将有权访问客户托管密钥设置期间选择的密钥保管库。 您可以了解有关[托管标识的更多信息](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，将删除对密钥保管库的访问，并且将不再访问使用客户密钥加密的任何数据。 依赖于此数据的任何功能都将停止工作。 您部署的任何模型也将取消部署。 所有上传的数据将从自定义转换器中删除。 如果托管标识已重新启用，我们不会自动为您重新部署模型。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户托管密钥时，托管标识将自动在封面下分配。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个 Azure AD 目录，则与资源关联的托管标识不会传输到新租户，因此客户托管密钥可能不再工作。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”****。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户管理的密钥存储在 Azure 密钥保管库

要启用客户管理的密钥，必须使用 Azure 密钥保管库来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性********。

认知服务加密仅支持大小为 2048 的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的“Key Vault 密钥”。****

> [!NOTE]
> 如果删除了整个密钥保管库，您的数据将不再显示，并且所有模型都将取消部署。 所有上传的数据将从自定义转换器中删除。 

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 或 [Azure 密钥保管库 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤消访问可有效地阻止对认知服务资源和模型中的所有数据的访问，因为认知服务无法访问加密密钥。 所有上传的数据也将从自定义转换器中删除。


## <a name="next-steps"></a>后续步骤

* [了解有关 Azure 密钥保管库的更多](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
