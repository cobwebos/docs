---
title: 开始使用 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 了解如何注册文本翻译 API 并获取订阅密钥。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 74a0a15490164cfdbc97529bec63a74ef63dbfc9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458459"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>如何注册文本翻译 API

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

- 没有帐户？ 可以创建[免费帐户](https://azure.microsoft.com/free/)进行试验，无需支付任何费用。
- 已有帐户？ [登录](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>创建文本翻译 API 的订阅

登录到门户后，可以创建文本翻译 API 的订阅，如下所示：

1. 选择“+ 创建资源”。
1. 在“搜索市场”搜索框中，输入“文本翻译”，然后从结果中选择它。
1. 选择“创建”，定义该订阅的详细信息。
1. 从“定价层”列表中，选择最适合需要的定价层。
    1. 每个订阅都有一个免费层。 免费层具有与付费计划相同的特征和功能，并且不会过期。
    1. 帐户只能有一个免费订阅。
1. 选择“创建”完成创建订阅。

## <a name="authentication-key"></a>身份验证密钥

注册文本翻译时，将获得订阅所特有的个性化访问密钥。 每次调用文本翻译 API 时都需要此密钥。

1. 通过先选择相应的订阅检索身份验证密钥。
1. 在订阅详细信息的“资源管理”部分中选择“密钥”。
1. 复制订阅所列出的任一密钥。

## <a name="learn-test-and-get-support"></a>了解、测试和获取支持

- [GitHub 上的代码示例](https://github.com/MicrosoftTranslator)
- [Microsoft Translator 支持论坛](https://www.aka.ms/TranslatorForum)

Microsoft Translator 通常会在验证订阅帐户状态前允许通过前几个请求。 如果前几个 Microsoft Translator API 请求成功，则调用失败，错误响应将指示问题。 请记录 API 响应，以便你能够查看原因。

## <a name="pricing-options"></a>定价选项

- [文本翻译 API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>自定义

使用自定义翻译以自定义翻译并创建符合自己的术语和风格的翻译系统，可从泛型 Microsoft Translator 神经机器翻译系统开始。 [了解详细信息](customization.md)

## <a name="additional-resources"></a>其他资源

- [Azure 入门（3 分钟视频）](https://azure.microsoft.com/get-started/?b=16.24)
- [如何通过发票付款](https://azure.microsoft.com/pricing/invoicing/)
