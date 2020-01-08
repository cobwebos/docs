---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 95639caddfa627290f4e7153d50f9f7799a1f320
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460549"
---
## <a name="protect-your-access-keys"></a>保护访问密钥

存储帐户访问密钥类似于存储帐户的根密码。 务必小心保护访问密钥。 使用 Azure Key Vault 来安全地管理和轮换密钥。 避免将访问密钥分发给其他用户、对其进行硬编码或将其以纯文本形式保存在其他用户可以访问的位置。 如果你认为密钥可能已泄漏，请对其进行轮替。

如果可能，请使用 Azure Active Directory （Azure AD）授权对 Blob 和队列存储而不是共享密钥的请求。 Azure AD 通过共享密钥提供更高的安全性和易用性。 有关使用 Azure AD 授权对数据的访问的详细信息，请参阅[使用 Azure Active Directory 授予对 Azure blob 和队列的访问权限](../articles/storage/common/storage-auth-aad.md)。
