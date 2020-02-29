---
title: 如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥 - Azure Key Vault | Microsoft Docs
description: 使用这篇文章可帮助你规划、生成然后传输自己的受 HSM 保护的密钥，以便与 Azure 密钥保管库一起使用。 也称为 BYOK 或自带密钥。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: d7f9527aa5aa3353dc9087f4bcc5f3a5fb241637
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184547"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>将受 HSM 保护的密钥导入 Key Vault

为了提高可靠性，在使用 Azure 密钥保管库时，可以在硬件安全模块 (HSM) 中导入或生成永不离开 HSM 边界的密钥。 这种情况通常被称为*自带密钥*，简称 BYOK。 Azure Key Vault 使用 Hsm （FIPS 140-2 Level 2）的 nCipher nShield 系列来保护密钥。

此功能不适用于 Azure 中国世纪互联。

> [!NOTE]
> 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)  
> 如需包括为受 HSM 保护的密钥创建密钥保管库的入门教程，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)。

## <a name="supported-hsms"></a>支持的 Hsm

通过两种不同的方法（具体取决于所使用的 Hsm）支持将受 HSM 保护的密钥传输到 Key Vault。 使用下表确定应该使用哪种方法来生成 Hsm，然后传输自己的受 HSM 保护的密钥，以便与 Azure Key Vault 一起使用。 

|HSM 供应商名称|支持的 HSM 模型|支持的 HSM 密钥传输方法|
|---|---|---|
|Thales|<ul><li>SafeNet Luna HSM 7 系列固件版本7.3 或更高版本</li></ul>| [使用 new BYOK 方法（预览版）](hsm-protected-keys-vendor-agnostic-byok.md)|
|nCipher|<ul><li>Hsm 系列 nShield</li></ul>|[使用旧的 BYOK 方法](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>后续步骤

按照[Key Vault 最佳实践](key-vault-best-practices.md)，确保密钥的安全性、持久性和监视。
