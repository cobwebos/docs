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
ms.openlocfilehash: 3b975d9f0953c874c627e30b382ad8c7fa7cfc09
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307789"
---
## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用符合[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [的256位 AES 加密对](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)数据进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 不过，你也可以通过自己的加密密钥来管理你的订阅。 客户托管的密钥（CMK）（也称为自带密钥（BYOK））提供了更大的灵活性，可用于创建、轮换、禁用和撤销访问控制。 还可以审核用于保护数据的加密密钥。