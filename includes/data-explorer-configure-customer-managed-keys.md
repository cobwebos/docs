---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297901"
---
Azure 数据资源管理器对静态存储帐户中的所有数据进行加密。 默认情况下，使用 Microsoft 管理的密钥对数据进行加密。 为了更进一步控制加密密钥，可以提供客户管理的密钥来用于对数据进行加密。 

客户管理的密钥必须存储在 [Azure Key Vault](/azure/key-vault/key-vault-overview) 中。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 Azure 数据资源管理器群集和密钥保管库必须在同一个区域中，但可以在不同的订阅中。 有关客户管理的密钥的详细说明，请参阅[使用 Azure 密钥保管库的客户管理密钥](/azure/storage/common/storage-service-encryption)。 

本文将介绍如何配置客户管理的密钥。

## <a name="configure-azure-key-vault"></a>配置 Azure Key Vault

要使用 Azure 数据资源管理器配置客户管理的密钥，必须在[密钥保管库上设置两个属性](/azure/key-vault/key-vault-ovw-soft-delete)：**软删除**和 **"不清除**"。 默认情况下未启用这些属性。 要启用这些属性，请在新密钥保管库或现有密钥保管库上执行启用[PowerShell](/azure/key-vault/key-vault-soft-delete-powershell)或[Azure CLI](/azure/key-vault/key-vault-soft-delete-cli)中的**软删除**和**启用清除保护**。 仅支持大小为 2048 的 RSA 密钥。 有关密钥的详细信息，请参阅[密钥保管库密钥](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)。

> [!NOTE]
> [先导和后继群集](/azure/data-explorer/follower)上不支持使用客户管理的密钥进行数据加密。 

## <a name="assign-an-identity-to-the-cluster"></a>为群集分配标识

若要为群集启用客户管理的密钥，请先将一个系统分配的托管标识分配给该群集。 你将使用此托管标识授予群集访问密钥保管库的权限。 若要配置系统分配的托管标识，请参阅[托管标识](/azure/data-explorer/managed-identities)。