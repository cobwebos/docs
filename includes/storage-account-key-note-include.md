---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86027319"
---
## <a name="protect-your-access-keys"></a>保护访问密钥

存储帐户访问密钥类似于存储帐户的根密码。 始终要小心保护访问密钥。 使用 Azure 密钥保管库安全地管理和轮换密钥。 避免将访问密钥分发给其他用户、对其进行硬编码或将其以纯文本形式保存在其他人可以访问的任何位置。 如果你认为访问密钥可能已泄露，请轮换密钥。

> [!NOTE]
> Microsoft 建议在可能的情况下，使用 Azure Active Directory (Azure AD) 对 blob 和队列数据的请求进行授权，而不是使用共享密钥。 Azure AD 通过共享密钥提供更高的安全性和易用性。 有关使用 Azure AD 授权访问数据的详细信息，请参阅[使用 Azure Active Directory 授权访问 Azure Blob 和队列](../articles/storage/common/storage-auth-aad.md)。
