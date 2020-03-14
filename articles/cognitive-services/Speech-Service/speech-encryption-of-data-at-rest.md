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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372355"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>静态数据的语音服务加密

当你将数据保存到云时，语音服务会自动对其进行加密。 语音服务加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用符合[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [的256位 AES 加密对](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)数据进行加密和解密。 加密和解密是透明的，这意味着对加密和访问进行管理。 默认情况下，你的数据是安全的，无需修改你的代码或应用程序即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

使用自定义语音和自定义语音时，语音服务可能会将以下数据存储在云中：  

* 语音跟踪数据-仅当你为自定义终结点启用跟踪时
* 已上传定型和测试数据

默认情况下，你的数据存储在 Microsoft 的存储中，你的订阅使用 Microsoft 托管的加密密钥。 你还可以选择准备你自己的存储帐户。 对应用商店的访问是由托管标识管理的，语音服务无法直接访问自己的数据，例如语音跟踪数据、自定义定型数据和自定义模型。

有关托管标识的详细信息，请参阅[什么是托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>自带存储（BYOS）以进行自定义和日志记录

若要请求访问自带存储，请填写并提交 [语音服务-自带存储（BYOS）请求表单](https://aka.ms/cogsvc-cmk)。 批准后，你需要创建自己的存储帐户来存储自定义和日志记录所需的数据。 添加存储帐户时，语音服务资源将启用系统分配的托管标识。 启用系统分配的托管标识后，此资源将注册到 Azure Active Directory （AAD）。 注册后，将向托管标识授予对存储帐户的访问权限。 可在此处了解有关托管标识的详细信息。 有关托管标识的详细信息，请参阅[什么是托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则将删除对存储帐户的访问权限。 这将导致需要访问存储帐户的语音服务部分停止工作。  

## <a name="regional-availability"></a>区域可用性

BYOS 目前在以下区域中提供：

* 美国南北部
* 美国西部 2
* 美国东部

## <a name="next-steps"></a>后续步骤

* [语音服务-自带存储（BYOS）请求表单](https://aka.ms/cogsvc-cmk)
* [什么是托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
