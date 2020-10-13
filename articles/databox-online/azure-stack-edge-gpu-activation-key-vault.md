---
title: Azure Key Vault 与 Azure Stack Edge 资源和设备激活的集成
description: 描述在 Azure Stack Edge Pro 设备的激活过程中 Azure Key Vault 如何与密钥管理关联。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: alkohli
ms.openlocfilehash: c841c96326f636e16f3b4f86fcb88a0962011c0f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976825"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Azure Key Vault 与 Azure Stack 边缘的集成 

Azure Key Vault 与用于机密管理的 Azure Stack Edge 资源集成。 本文提供了有关如何在设备激活期间为 Azure Stack Edge 资源创建 Azure Key Vault 的详细信息，然后将其用于密钥管理。 


## <a name="about-key-vault-and-azure-stack-edge"></a>关于 Key vault 和 Azure Stack Edge

Azure Key Vault 云服务用于安全地存储和控制对令牌、密码、证书、API 密钥和其他机密的访问。 Key Vault 还可以轻松地创建和控制用于对数据进行加密的加密密钥。 

对于 Azure Stack Edge 服务，使用的机密之一是通道完整性密钥 (CIK) 。 此密钥用于加密机密。 集成 key vault 后，CIK 会安全地存储在密钥保管库中。 有关详细信息，请参阅 [安全地存储机密和密钥](../key-vault/general/overview.md#securely-store-secrets-and-keys)。


## <a name="key-vault-creation"></a>创建密钥保管库

在激活密钥生成过程中，将为 Azure Stack Edge 资源创建密钥保管库。 

- 创建 Azure Stack Edge 资源时，需要注册 *KeyVault* 资源提供程序。 如果你具有订阅的所有者或参与者访问权限，则会自动注册资源提供程序。 将在同一订阅中创建密钥保管库，并在资源组中创建 Azure Stack Edge 资源。 

- 创建 Azure Stack Edge 资源时，还会创建一个托管服务标识 (MSI) ，该在资源的生存期内保持不变，并与云中的资源提供程序通信。 

    启用 MSI 后，Azure 将为 Azure Stack Edge 资源创建受信任的标识。

- 创建 Azure Stack Edge 资源并从 Azure 门户生成激活密钥后，将创建一个密钥保管库。 此密钥保管库用于机密管理，只要存在 Azure Stack Edge 资源，就会保留该密钥保管库。 

    ![在激活密钥生成过程中创建的 Key Vault](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- 你可以选择接受默认密钥名称或指定密钥保管库的自定义名称。 密钥保管库名称长度必须介于3到24个字符之间。 不能使用已在使用中的密钥保管库。 <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![创建 Azure Stack Edge 资源期间创建的 MSI](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- 若要防止意外删除，在密钥保管库上启用了资源锁。 还会在密钥保管库上启用软删除，如果意外删除，则允许在90天内还原密钥保管库。 有关详细信息，请参阅 [Azure Key Vault 软删除概述](../key-vault/general/soft-delete-overview.md)

    如果意外删除密钥保管库，且未超过90天的清除保护持续时间，请按照以下步骤来 [恢复密钥保管库](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)。 

- 如果在将 Azure Key Vault 与 Azure Stack Edge 资源集成之前现有 Azure Stack Edge 资源，则不会受到影响。 你可以继续使用现有的 Azure Stack Edge 资源。 

- 删除 Azure Stack Edge 资源后，还会随资源一起删除 Azure Key Vault。 系统会提示进行确认。 如果不想删除此密钥保管库，可以选择不提供同意。 仅删除 Azure Stack Edge 资源，使密钥保管库保持不变。 

- 如果此密钥保管库用于存储其他密钥，则你仍可以在删除90天内还原。 在清除保护期间，不能使用密钥保管库名称来创建新的密钥保管库。

如果遇到与密钥保管库和设备激活相关的任何问题，请参阅 [解决设备激活问题](azure-stack-edge-gpu-troubleshoot-activation.md)。

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>后续步骤

- 了解有关如何 [生成激活密钥](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)的详细信息。

