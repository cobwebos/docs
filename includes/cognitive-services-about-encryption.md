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
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372168"
---
## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)兼容[的 256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密对数据进行加密和解密。 加密和解密是透明的，这意味着加密和访问是为您管理的。 默认情况下，您的数据是安全的，您无需修改代码或应用程序就可以利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，您的订阅使用 Microsoft 管理的加密密钥。 如果使用的是支持客户管理的密钥的定价层，则可以在[Azure 门户](https://portal.azure.com)的 **"加密"** 部分中查看资源的加密设置，如下图所示。

![查看加密设置](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

对于仅支持 Microsoft 管理的加密密钥的订阅，您将没有**加密**部分。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥与 Azure Key Vault

还有一个选项可以用自己的密钥管理订阅。 客户管理的密钥 （CMK）也称为"自带密钥 （BYOK"），为创建、旋转、禁用和撤销访问控制提供了更大的灵活性。 还可以审核用于保护数据的加密密钥。
