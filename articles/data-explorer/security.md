---
title: 在 Azure 中保护 Azure 数据资源管理器群集
description: 了解如何保护 Azure 数据资源管理器中的群集。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: cc16a40b0ea53d433a5a6c592f3b9ea364ef9089
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725827"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>在 Azure 中保护 Azure 数据资源管理器群集

本文介绍 Azure 数据资源管理器中的安全性，以帮助你保护云中的数据和资源，并满足业务的安全需求。 确保群集安全非常重要。 保护群集包含一个或多个包含安全访问和存储的 Azure 功能。 本文提供的信息可帮助你确保群集的安全。

## <a name="managed-identities-for-azure-resources"></a>Azure 资源的托管标识

构建云应用程序时，常见的难题是代码中用于对云服务进行身份验证的凭据管理。 保护这些凭据是一项重要任务。 这些凭据不应存储在开发人员工作站中或签入源控件中。 虽然 Azure Key Vault 可用于安全存储凭据、机密以及其他密钥，但代码需要通过 Key Vault 的身份验证才能检索它们。

Azure 资源的 Azure Active Directory （Azure AD）托管标识功能解决了此问题。 此功能为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。 有关此服务的详细信息，请参阅[Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)概述页。

## <a name="data-encryption"></a>数据加密

### <a name="azure-disk-encryption"></a>Azure 磁盘加密

[Azure 磁盘加密](/azure/security/azure-security-disk-encryption-overview)有助于保护和保护数据，以满足组织的安全性和符合性承诺。 它为群集虚拟机的 OS 和数据磁盘提供卷加密。 Azure 磁盘加密还与[Azure Key Vault](/azure/key-vault/)集成，使我们能够控制和管理磁盘加密密钥和机密，并确保 VM 磁盘上的所有数据都已加密。 

### <a name="customer-managed-keys-with-azure-key-vault"></a>客户托管的密钥与 Azure Key Vault

默认情况下，使用 Microsoft 托管的密钥对数据进行加密。 为了进一步控制加密密钥，你可以提供客户管理的密钥用于数据加密。 你可以用自己的密钥管理存储级别的数据加密。 使用客户管理的密钥来保护和控制对根加密密钥（用于加密和解密所有数据）的访问。 客户管理的密钥提供更大的灵活性，以创建、轮换、禁用和撤消访问控制。 你还可以审核用于保护数据的加密密钥。

使用 Azure Key Vault 来存储客户管理的密钥。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault API 来生成密钥。 Azure 数据资源管理器群集和 Azure Key Vault 必须位于同一区域中，但它们可以位于不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](/azure/key-vault/key-vault-overview)。 有关客户托管密钥的详细说明，请参阅[Azure Key Vault 的客户托管密钥](/azure/storage/common/storage-service-encryption)

> [!Note]
> 客户托管的密钥依赖于 Azure 资源的托管标识，一项功能 Azure Active Directory （Azure AD）。 若要在 Azure 门户中配置客户管理的密钥，需要配置群集的**SystemAssigned**托管标识。

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户托管的密钥存储在 Azure Key Vault

若要在群集上启用客户管理的密钥，请使用 Azure Key Vault 来存储密钥。 必须同时启用**软删除**和不**清除**密钥保管库中的属性。 密钥保管库必须与群集位于同一订阅中。 Azure 数据资源管理器使用 Azure 资源的托管标识向密钥保管库进行身份验证，以便进行加密和解密操作。 托管标识不支持跨目录方案。

#### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

你可以根据你的符合性策略，在 Azure Key Vault 中轮替客户管理的密钥。 当密钥旋转时，必须更新群集以使用新的密钥 URI。 轮换密钥并不会触发重新加密群集中的数据。 

#### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅[Azure Key Vault PowerShell](/powershell/module/az.keyvault/)或[Azure Key Vault CLI](/cli/azure/keyvault)。 撤消访问会阻止访问群集的存储级别中的所有数据，因为 Azure 数据资源管理器无法访问加密密钥。

> [!Note]
> 当 Azure 数据资源管理器确定对客户管理的密钥的访问权限被吊销时，它会自动挂起群集以删除任何缓存的数据。 返回对密钥的访问权限后，需要手动恢复群集。

## <a name="role-based-access-control"></a>基于角色的访问控制

使用[基于角色的访问控制（RBAC）](/azure/role-based-access-control/overview)，可以在团队中对职责进行隔离，只向群集用户授予所需的访问权限。 只允许特定操作，而不是为每个人提供对群集的无限制权限。 您可以使用[Azure CLI](/azure/role-based-access-control/role-assignments-cli)或[Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)为[Azure 门户](/azure/role-based-access-control/role-assignments-portal)中[的数据库配置访问控制](/azure/data-explorer/manage-database-permissions)。

## <a name="next-steps"></a>后续步骤

* [配置 Azure 数据资源管理器群集的托管标识](managed-identities.md)
* 通过启用静态加密来[保护 Azure 中的群集数据资源管理器门户](manage-cluster-security.md)。
* [使用 Azure 资源管理器模板配置客户托管密钥](customer-managed-keys-resource-manager.md)
* [使用配置客户管理的密钥C#](customer-managed-keys-csharp.md)

