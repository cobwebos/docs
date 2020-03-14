---
title: 使用 Azure 门户来配置客户管理的密钥
titleSuffix: Cognitive Services
description: 了解如何使用 Azure 门户配置 Azure Key Vault 客户管理的密钥。 使用客户管理的密钥可以创建、轮换、禁用和撤消访问控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: ad97bde447a7bfbddca480b8561403f2cee7e25a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372278"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>使用 Azure 门户配置客户管理的密钥 Azure Key Vault

必须使用 Azure Key Vault 来存储客户管理的密钥。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault Api 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域和相同的 Azure Active Directory （Azure AD）租户中，但它们可以位于不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

本文说明如何使用[Azure 门户](https://portal.azure.com/)配置使用客户管理的密钥的 Azure Key Vault。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户从 Azure Key Vault 设置和检索机密](../../key-vault/quick-create-portal.md)。

## <a name="configure-azure-key-vault"></a>配置 Azure Key Vault

使用客户托管的密钥需要在密钥保管库上设置两个属性，**软删除**并不**清除**。 默认情况下不启用这些属性，但可以使用 PowerShell 或 Azure CLI 在新的或现有的密钥保管库上启用。

> [!IMPORTANT]
> 如果你没有 "**软删除**" 和 "**未**启用启用属性" 并删除你的密钥，则无法恢复认知服务资源中的数据。

若要了解如何在现有的密钥保管库上启用这些属性，请参阅以下文章之一中标题为**启用软删除**和**启用清除保护**的部分：

- [如何将软删除与 PowerShell 配合使用](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)。
- [如何将软删除与 CLI 配合使用](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli)。

Azure 存储加密仅支持大小为2048的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**Key Vault 密钥**。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

若要在 Azure 门户中启用客户托管的密钥，请执行以下步骤：

1. 导航到认知服务资源。
1. 在认知服务资源的 "**设置**" 边栏选项卡上，单击 "**加密**"。 选择 "**客户托管密钥**" 选项，如下图所示。

    ![显示如何选择客户托管密钥的屏幕截图](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>指定密钥

启用客户管理的密钥后，你将有机会指定与认知服务资源关联的密钥。

### <a name="specify-a-key-as-a-uri"></a>将密钥指定为 URI

若要将密钥指定为 URI，请执行以下步骤：

1. 若要在 Azure 门户中查找密钥 URI，请导航到密钥保管库，并选择 "**密钥**" 设置。 选择所需的密钥，然后单击该密钥以查看其版本。 选择密钥版本以查看该版本的设置。
1. 复制提供 URI 的 "**密钥标识符**" 字段的值。

    ![显示密钥保管库密钥 URI 的屏幕截图](../media/cognitive-services-encryption/key-uri-portal.png)

1. 在存储帐户的 "**加密**设置" 中，选择 "**输入密钥 URI** " 选项。
1. 将复制的 URI 粘贴到 "**密钥 URI** " 字段。

   ![显示如何输入密钥 URI 的屏幕截图](../media/cognitive-services-encryption/ssecmk2.png)

1. 指定包含密钥保管库的订阅。
1. 保存所做更改。

### <a name="specify-a-key-from-a-key-vault"></a>从 Key Vault 指定密钥

若要指定密钥保管库中的密钥，请首先确保你有包含密钥的密钥保管库。 若要指定密钥保管库中的密钥，请执行以下步骤：

1. 选择“从 Key Vault 中选择”选项。
1. 选择包含要使用的密钥的密钥保管库。
1. 从 key vault 中选择密钥。

   ![显示客户托管密钥选项的屏幕截图](../media/cognitive-services-encryption/ssecmk3.png)

1. 保存所做更改。

## <a name="update-the-key-version"></a>更新密钥版本

当你创建新的密钥版本时，请更新认知服务资源以使用新版本。 执行以下步骤:

1. 导航到认知服务资源并显示**加密**设置。
1. 为新的密钥版本输入 URI。 或者，你可以再次选择密钥保管库和密钥来更新版本。
1. 保存所做更改。

## <a name="use-a-different-key"></a>使用其他密钥

若要更改用于加密的密钥，请执行以下步骤：

1. 导航到认知服务资源并显示**加密**设置。
1. 输入新密钥的 URI。 或者，你可以选择密钥保管库并选择一个新密钥。
1. 保存所做更改。

## <a name="disable-customer-managed-keys"></a>禁用客户管理的密钥

当你禁用客户管理的密钥时，你的认知服务资源将通过 Microsoft 托管的密钥进行加密。 若要禁用客户托管的密钥，请执行以下步骤：

1. 导航到认知服务资源并显示**加密**设置。
1. 取消选中 "**使用自己的密钥**" 设置旁边的复选框。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
* [认知服务客户托管的密钥请求表单](https://aka.ms/cogsvc-cmk)
* [静态数据的人脸服务加密](../Face/face-encryption-of-data-at-rest.md)
* [静态数据加密 QnA Maker](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [语言理解静态数据的服务加密](../LUIS/luis-encryption-of-data-at-rest.md)
* [静态数据加密内容审查器](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [静态数据的转换器加密](../translator/translator-encryption-of-data-at-rest.md)
