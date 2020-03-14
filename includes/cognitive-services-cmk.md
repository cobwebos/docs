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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372190"
---
### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

必须使用 Azure Key Vault 来存储客户管理的密钥。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault Api 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域和相同的 Azure Active Directory （Azure AD）租户中，但它们可以位于不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

新的认知服务资源始终使用 Microsoft 托管的密钥进行加密。 创建资源时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，此访问策略向与认知服务资源关联的托管标识授予密钥权限。 只有使用 CMK 定价层创建了资源后，托管标识才可用。

若要了解如何将客户托管的密钥用于认知服务加密的 Azure Key Vault，请参阅：

- [将客户托管的密钥配置 Key Vault 用于认知服务加密的 Azure 门户](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)

启用客户托管的密钥还将启用系统分配的托管标识，一项功能 Azure AD。 启用系统分配的托管标识后，此资源将注册 Azure Active Directory。 注册后，将向托管标识授予对客户管理密钥安装过程中选择的 Key Vault 的访问权限。 你可以了解有关[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的详细信息。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则将删除对密钥保管库的访问权限，并且任何使用客户密钥加密的数据将无法再访问。 所有依赖于此数据的功能都将停止工作。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，会自动在这些内容下分配托管标识。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个目录，则与该资源关联的托管标识不会传输到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅在常见问题中**传输 Azure AD 目录之间的订阅**[和 Azure 资源的托管标识的已知问题](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户托管的密钥存储在 Azure Key Vault

若要启用客户管理的密钥，必须使用 Azure Key Vault 来存储密钥。 必须同时启用**软删除**和不**清除**密钥保管库中的属性。

认知服务加密仅支持大小为2048的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)中的**Key Vault 密钥**。

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

你可以根据你的符合性策略，在 Azure Key Vault 中轮替客户管理的密钥。 当密钥旋转时，必须更新认知服务资源才能使用新密钥 URI。 若要了解如何更新资源以在 Azure 门户中使用新版本的密钥，请参阅[使用 Azure 门户为认知服务配置客户管理的密钥](..\articles\cognitive-services\Encryption\cognitive-services-encryption-keys-portal.md)中的 "**更新密钥版本**" 一节。

旋转密钥不会触发重新加密资源中的数据。 用户无需执行任何其他操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//)或[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤消访问权限会有效地阻止访问认知服务资源中的所有数据，因为认知服务无法访问加密密钥。


