---
title: 如何生成和传输受 HSM 保护的密钥 - Azure Key Vault
description: 了解如何规划、生成，然后传输自己的受 HSM 保护的密钥供 Azure Key Vault 使用。 也称为 BYOK 或自带密钥。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 33249da6772d146de3e5e7351bc82c203674ddff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532131"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>将 HSM 保护的密钥导入 Key Vault

为了提高可靠性，在使用 Azure 密钥保管库时，可以在硬件安全模块 (HSM) 中导入或生成永不离开 HSM 边界的密钥。 这种情况通常被称为自带密钥，简称 BYOK。 Azure Key Vault 使用 HSM 的 nCipher nShield 系列（FIPS 140-2 第 2 级验证）来保护密钥。

此功能不适用于 Azure 中国世纪互联。

> [!NOTE]
> 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../general/overview.md)  
> 如需包括为受 HSM 保护的密钥创建密钥保管库的入门教程，请参阅[什么是 Azure 密钥保管库？](../general/overview.md)。

## <a name="supported-hsms"></a>支持的 HSM

通过两种不同的方法（具体取决于所使用的 HSM）支持将受 HSM 保护的密钥传输到 Key Vault。 使用下表确定应该对 HSM 使用哪种方法来生成和传输你自己的受 HSM 保护的密钥，以便将其与 Azure Key Vault 一起使用。 

|供应商名称|供应商类型|支持的 HSM 模型|支持的 HSM 密钥传输方法|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|制造商，<br/>HSM 即服务|<ul><li>HSM 的 nShield 系列</li><li>nShield 即服务</ul>|方法 1：[nCipher BYOK](hsm-protected-keys-ncipher.md)（具有用于密钥导入和 HSM 验证的强证明）<br/>方法 2：[使用新的 BYOK 方法](hsm-protected-keys-byok.md) |
|Thales|制造商|<ul><li>固件版本为 7.3 或更高版本的 Luna HSM 7 系列</li></ul>| [使用新的 BYOK 方法](hsm-protected-keys-byok.md)|
|Fortanix|制造商，<br/>HSM 即服务|<ul><li>自防御密钥管理服务 (SDKMS)</li><li>Equinix SmartKey</li></ul>|[使用新的 BYOK 方法](hsm-protected-keys-byok.md)|
|Marvell|制造商|所有具有以下固件版本的 LiquidSecurity HSM<ul><li>固件版本 2.0.4 或更高版本</li><li>固件版本 3.2 或更高版本</li></ul>|[使用新的 BYOK 方法](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV（企业密钥管理系统）|多个 HSM 品牌和型号，包括<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>有关详细信息，请参阅 [Cryptomathic 站点](https://www.cryptomathic.com/azurebyok)|[使用新的 BYOK 方法](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>后续步骤

* 按照 [Key Vault 最佳做法](../general/best-practices.md)，确保密钥的安全性、持久性和监视性。
* 有关新的 BYOK 方法的完整说明，请参阅 [BYOK 规范](https://docs.microsoft.com/azure/key-vault/keys/byok-specification)
