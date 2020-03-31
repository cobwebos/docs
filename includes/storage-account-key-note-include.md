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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460549"
---
## <a name="protect-your-access-keys"></a>保护访问密钥

存储帐户访问密钥类似于存储帐户的根密码。 始终要小心保护访问密钥。 使用 Azure 密钥保管库安全地管理和轮换密钥。 避免将访问密钥分发给其他用户、对其进行硬编码或将其以纯文本形式保存在其他人可以访问的任何位置。 如果你认为访问密钥可能已泄露，请轮换密钥。

如果可能，请使用 Azure Active Directory (Azure AD) 而不是共享密钥来为针对 Blob 和队列存储的请求授权。 Azure AD 通过共享密钥提供更高的安全性和易用性。 有关使用 Azure AD 授权访问数据的详细信息，请参阅[使用 Azure Active Directory 授权访问 Azure Blob 和队列](../articles/storage/common/storage-auth-aad.md)。
