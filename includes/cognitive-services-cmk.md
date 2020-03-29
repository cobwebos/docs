---
title: include 文件
description: include 文件
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: e5e1755e6351d308c041de5cefe943e9874c8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372190"
---
### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域和同一 Azure 活动目录 （Azure AD） 租户中，但它们可以位于不同的订阅中。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

新的认知服务资源始终使用 Microsoft 管理的密钥进行加密。 在创建资源时无法启用客户管理的密钥。 客户管理的密钥存储在 Azure 密钥保管库中，并且必须预配密钥保管库，这些访问策略向与认知服务资源关联的托管标识授予密钥权限。 只有在使用 CMK 的定价层创建资源后，托管标识才可用。

要了解如何将客户管理的密钥与 Azure 密钥保管库用于认知服务加密，请参阅：

- [使用密钥保管库配置客户管理的密钥，以便从 Azure 门户进行认知服务加密](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

启用客户托管密钥还将启用系统分配的托管标识，这是 Azure AD 的一项功能。 启用系统分配的托管标识后，此资源将注册到 Azure 活动目录。 注册后，托管标识将有权访问客户托管密钥设置期间选择的密钥保管库。 您可以了解有关[托管标识的更多信息](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，将删除对密钥保管库的访问，并且将不再访问使用客户密钥加密的任何数据。 依赖于此数据的任何功能都将停止工作。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户托管密钥时，托管标识将自动在封面下分配。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个 Azure AD 目录，则与资源关联的托管标识不会传输到新租户，因此客户托管密钥可能不再工作。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”****。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户管理的密钥存储在 Azure 密钥保管库

要启用客户管理的密钥，必须使用 Azure 密钥保管库来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性********。

认知服务加密仅支持大小为 2048 的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的“Key Vault 密钥”。****

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 旋转密钥时，必须更新认知服务资源才能使用新的密钥 URI。 要了解如何更新资源以在 Azure 门户中使用密钥的新版本，请参阅[使用 Azure 门户在"配置认知服务的客户管理密钥"](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)中**更新密钥版本的**部分。

旋转密钥不会触发资源中数据的重新加密。 用户无需执行任何其他操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 或 [Azure 密钥保管库 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤消访问可有效地阻止对认知服务资源中的所有数据的访问，因为认知服务无法访问加密密钥。


