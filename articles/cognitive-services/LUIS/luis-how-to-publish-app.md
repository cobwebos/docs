---
title: Publish app - LUIS
titleSuffix: Azure Cognitive Services
description: 当完成生成和测试活动 LUIS 应用时，可通过将其发布到终结点使其可用于客户端应用程序。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: f213c1d43930075c78cf81de345f612e46bbfb1c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221723"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>将活动的已训练应用发布到过渡或生产终结点

When you finish building, training, and testing your active LUIS app, make it available to your client application by publishing it to the endpoint. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>发布

1. 若要发布到终结点，请在右上方的面板中选择“发布”。 

    ![Publish button in top, right nav bar](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Select your settings for the published prediction endpoint, then select **Publish**.

    ![Select publish settings then select Publish button](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publishing slots

Select the correct slot when the pop-up window displays: 

* 过渡
* 生产 

By using both publishing slots, this allows you to have two different versions of your app available at the published endpoints or the same version on two different endpoints. 

### <a name="publishing-regions"></a>发布区域

The app is published to all regions associated with the LUIS prediction endpoint resources added in the LUIS portal from the **Manage** ->  **[Azure Resources](/luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** page. 

For example, for an app created on [www.luis.ai](https://www.luis.ai), if you create a LUIS resource in two regions, **westus** and **eastus**, and add these to the app as resources, the app is published in both regions. 有关 LUIS 区域的详细信息，请参阅[区域](luis-reference-regions.md)。

> [!TIP]
> There are 3 authoring regions. You must author in the region you intend to publish to. If you need to publish to all regions, you need to manage your authoring process and the resulting trained model in all 3 authoring regions. 


## <a name="configuring-publish-settings"></a>配置发布设置

After you select the slot, configure the publish settings for:

* 观点分析
* Spelling correction - v2 prediction endpoint only
* 语音启动 

After you publish, these settings are available for review from the **Manage** section's **Publish settings** page. You can change the settings with every publish. If you cancel a publish, any changes you made during the publish are also canceled. 

### <a name="when-your-app-is-published"></a>When your app is published

When your app is successfully published, a success notification appears at the top of the browser. The notification also includes a link to the endpoints. 

如果需要终结点 URL，请选择该链接。 You can also get to the endpoint URLs by selecting **Manage** in the top menu, then select **Azure Resources** in the left menu. 

## <a name="sentiment-analysis"></a>观点分析

<a name="enable-sentiment-analysis"></a>

情绪分析使 LUIS 可以与[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)集成，以便提供情绪和关键短语分析。 

不需提供文本分析密钥，而且系统不会因为此服务向你的 Azure 帐户收费。 

情绪数据是一个介于 0 到 1 之间的分数，指示数据的正面情绪（分数接近 1）或负面情绪（分数接近 0）。 `positive`、`neutral` 和 `negative` 的情绪标签针对每个支持的区域性。 目前，仅英语支持情绪标签。 

若要详细了解使用情绪分析时的 JSON 终结点响应，请参阅[情绪分析](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Spelling correction

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Corrections to spelling are made before the LUIS user utterance prediction. You can see any changes to the original utterance, including spelling, in the response.

## <a name="speech-priming"></a>语音启动

Speech priming is the process of using sending the LUIS model to Speech services prior to conversion of text to speech. This allows the speech service to provide speech conversion more accurately for your model. This allows bot Speech and LUIS requests and responses in one call by making one speech call and getting back a LUIS response. It provides less latency overall.

## <a name="next-steps"></a>后续步骤

* 请参阅[管理密钥](./luis-how-to-azure-subscription.md)以了解如何将密钥添加到针对 LUIS 的 Azure 订阅密钥、如何设置必应拼写检查密钥以及如何将所有意向包含在结果中。
* 有关如何在测试控制台中测试已发布应用的说明，请参阅[训练和测试应用](luis-interactive-test.md)。

