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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372168"
---
## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用符合[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [的256位 AES 加密对](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)数据进行加密和解密。 加密和解密是透明的，这意味着对加密和访问进行管理。 默认情况下，你的数据是安全的，无需修改你的代码或应用程序即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 如果你使用的定价层支持客户管理的密钥，则可以在[Azure 门户](https://portal.azure.com)的 "**加密**" 部分中看到资源的加密设置，如下图所示。

![查看加密设置](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

对于仅支持 Microsoft 托管的加密密钥的订阅，你将没有**加密**部分。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户托管的密钥与 Azure Key Vault

还可以选择使用自己的密钥来管理订阅。 客户托管的密钥（CMK）（也称为自带密钥（BYOK））提供了更大的灵活性，可用于创建、轮换、禁用和撤销访问控制。 你还可以审核用于保护数据的加密密钥。
