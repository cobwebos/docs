---
title: 入门-转换器
titleSuffix: Azure Cognitive Services
description: 本文将演示如何注册 Azure 认知服务翻译员并获取订阅密钥。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 15204a081f001d421574daa7f5ec2bac40152a19
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995745"
---
# <a name="how-to-sign-up-for-translator"></a>如何注册翻译人员

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

- 没有帐户？ 可以创建[免费帐户](https://azure.microsoft.com/free/)进行试验，无需支付任何费用。
- 已有帐户？ [登录](https://ms.portal.azure.com/)

## <a name="create-a-subscription-for-translator"></a>为翻译人员创建订阅

登录到门户后，可以按如下所示创建一个对转换器的订阅：

1. 选择 " **+ 创建资源**"。
1. 在 "**搜索 Marketplace"** 搜索框中输入 "**转换器**"，然后从结果中选择它。
1. 选择“创建”，定义该订阅的详细信息****。
1. 从“定价层”列表中，选择最适合需要的定价层****。
    1. 每个订阅都有一个免费层。 免费层具有与付费计划相同的特征和功能，并且不会过期。
    1. 帐户只能有一个免费订阅。
1. 选择“创建”完成创建订阅****。

## <a name="authentication-key"></a>身份验证密钥

注册转换器时，你将获得订阅独有的个性化访问密钥。 每次调用转换器时都需要此密钥。

1. 通过先选择相应的订阅检索身份验证密钥。
1. 在订阅详细信息的“资源管理”部分中选择“密钥”********。
1. 复制订阅所列出的任一密钥。

## <a name="learn-test-and-get-support"></a>了解、测试和获取支持

- [GitHub 上的代码示例](https://github.com/MicrosoftTranslator)
- [Microsoft Translator 支持论坛](https://www.aka.ms/TranslatorForum)

Microsoft Translator 通常会在验证订阅帐户状态前允许通过前几个请求。 如果前几个翻译人员请求成功，则调用失败，错误响应将指示问题。 请记录 API 响应，以便你能够查看原因。

## <a name="pricing-options"></a>定价选项

- [翻译](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>自定义

使用自定义翻译以自定义翻译并创建符合自己的术语和风格的翻译系统，可从泛型 Microsoft Translator 神经机器翻译系统开始。 [了解详细信息](customization.md)

## <a name="additional-resources"></a>其他资源

- [Azure 入门（3 分钟视频）](https://azure.microsoft.com/get-started/?b=16.24)
- [如何通过发票付款](https://azure.microsoft.com/pricing/invoicing/)
