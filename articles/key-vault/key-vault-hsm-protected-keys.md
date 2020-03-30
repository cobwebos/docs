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
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082891"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>将受 HSM 保护的密钥导入密钥保管库

为了提高可靠性，在使用 Azure 密钥保管库时，可以在硬件安全模块 (HSM) 中导入或生成永不离开 HSM 边界的密钥。 这种情况通常被称为*自带密钥*，简称 BYOK。 Azure 密钥保管库使用 nCipher nShield 系列 HSM（FIPS 140-2 级别 2 验证）来保护密钥。

此功能不适用于 Azure 中国 21Vianet。

> [!NOTE]
> 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)  
> 如需包括为受 HSM 保护的密钥创建密钥保管库的入门教程，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)。

## <a name="supported-hsms"></a>支持 HSM

根据您使用的 HSM，可通过两种不同的方法将受 HSM 保护的密钥传输到密钥保管库。 使用下表确定应该使用哪种方法来生成 HSM，然后传输自己的受 HSM 保护的密钥，以便与 Azure 密钥保管库一起使用。 

|供应商名称|供应商类型|支持的 HSM 型号|支持的 HSM 密钥传输方法|
|---|---|---|---|
|n 奇普赫|制造商|<ul><li>nShield 系列 HSM</li></ul>|[使用旧 BYOK 方法](hsm-protected-keys-legacy.md)|
|泰莱斯|制造商|<ul><li>SafeNet Luna HSM 7 系列，固件版本 7.3 或更新</li></ul>| [使用新的 BYOK 方法（预览）](hsm-protected-keys-vendor-agnostic-byok.md)|
|福塔尼克斯|HSM 作为服务|<ul><li>自防密钥管理服务 （SDKMS）</li></ul>|[使用新的 BYOK 方法（预览）](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>后续步骤

遵循[关键保管库最佳实践](key-vault-best-practices.md)，以确保密钥的安全性、耐用性和监控。
