---
title: 静态数据的语音服务加密
titleSuffix: Azure Cognitive Services
description: 静态数据的语音服务加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372355"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>静态数据的语音服务加密

语音服务在将数据保存到云中时自动加密数据。 语音服务加密可保护您的数据，并帮助您履行组织安全和合规性承诺。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)兼容[的 256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)加密对数据进行加密和解密。 加密和解密是透明的，这意味着加密和访问是为您管理的。 默认情况下，您的数据是安全的，您无需修改代码或应用程序就可以利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

当您使用自定义语音和自定义语音时，语音服务可能会在云中存储以下数据：  

* 语音跟踪数据 - 仅当打开自定义终结点的跟踪时
* 上传的培训和测试数据

默认情况下，您的数据存储在 Microsoft 的存储中，您的订阅使用 Microsoft 管理的加密密钥。 您还可以选择准备自己的存储帐户。 对存储的访问由托管标识管理，语音服务不能直接访问您自己的数据，如语音跟踪数据、自定义训练数据和自定义模型。

有关托管标识的详细信息，请参阅[什么是托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>自带存储 （BYOS） 进行自定义和日志记录

要请求访问以自带存储，请填写并提交 [语音服务 - 自带存储 （BYOS） 请求表](https://aka.ms/cogsvc-cmk)。 获得批准后，您需要创建自己的存储帐户来存储自定义和日志记录所需的数据。 添加存储帐户时，语音服务资源将启用系统分配的托管标识。 启用系统分配的托管标识后，此资源将注册到 Azure 活动目录 （AAD）。 注册后，托管标识将被授予对存储帐户的访问权限。 您可以在此处了解有关托管标识的更多信息。 有关托管标识的详细信息，请参阅[什么是托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，将删除对存储帐户的访问。 这将导致需要访问存储帐户的语音服务的某些部分停止工作。  

## <a name="regional-availability"></a>区域可用性

BYOS 目前在以下区域可用：

* 美国中南部
* 美国西部 2
* 美国东部

## <a name="next-steps"></a>后续步骤

* [语音服务 - 自带存储 （BYOS） 请求表](https://aka.ms/cogsvc-cmk)
* [什么是托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
