---
title: 在 Azure 门户中创建认知服务资源
titleSuffix: Azure Cognitive Services
description: 通过在 Azure 门户中创建和订阅资源，开始使用 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: 33200f8807ad28ac160bb377b1107548f4e42d51
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590802"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>使用 Azure 门户创建认知服务资源

使用此快速入门开始使用 Azure 认知服务。 在 Azure 门户中创建认知服务资源后，你将获得用于验证应用程序的终结点和密钥。


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>必备条件

* 有效的 Azure 订阅-[免费创建一个](https://azure.microsoft.com/free/)。

## <a name="create-a-new-azure-cognitive-services-resource"></a>新建 Azure 认知服务资源

1. 创建资源。

    #### <a name="multi-service-resource"></a>[多服务资源](#tab/multiservice)
    
    多服务资源在门户中名为“认知服务”  。 [创建认知服务资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)。
    
    此时，多服务资源允许访问以下认知服务：
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | 计算机视觉  | 内容审查器                                    | 人脸               | 语言理解 (LUIS) | 文本分析   |
    | 翻译工具  | 必应搜索 v7 <br>（Web、图像、新闻、视频、视觉对象） | 必应自定义搜索 | 必应实体搜索            | 必应自动建议 |
    | 必应拼写检查 |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[单服务资源](#tab/singleservice)

    使用以下链接为可用的认知服务创建资源：

    | 影像                      | 语音                  | 语言                          | 决策             | 搜索                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [计算机视觉](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [语音服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [沉浸式读者](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [异常检测器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [必应搜索 API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [自定义视觉服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [说话人识别](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [语言理解 (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [内容审查器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [必应自定义搜索](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [人脸](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [个性化体验创建服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [必应实体搜索](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [墨迹识别器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [文本分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [必应拼写检查](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [翻译工具](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [必应自动建议](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. 在“创建”页中提供以下信息： ****

    #### <a name="multi-service-resource"></a>[多服务资源](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | 认知服务资源的描述性名称。 例如，*MyCognitiveServicesResource*。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 Azure 资源组。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    单击 **“创建”** 。

    #### <a name="single-service-resource"></a>[单服务资源](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | 认知服务资源的描述性名称。 例如，*TextAnalyticsResource*。 |
    | **订阅** | 选择一个可用的 Azure 订阅。 |
    | **位置** | 认知服务实例的位置。 不同位置可能会导致延迟，但不会影响资源的运行时可用性。 |
    | **定价层** | 认知服务帐户的费用取决于你所选的选项和你的使用情况。 有关详细信息，请参阅 API [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **资源组** | 将包含认知服务资源的 Azure 资源组。 可以创建新组或将其添加到预先存在的组。 |

    ![“创建资源”屏幕](media/cognitive-services-apis-create-account/resource_create_screen.png)

    单击 **“创建”** 。

    ***


## <a name="get-the-keys-for-your-resource"></a>获取资源的密钥

1. 成功部署资源后，单击“后续步骤”**** 下的“转到资源”****。

    ![搜索“认知服务”](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 从打开的快速入门窗格中，可以访问密钥和终结点。

    ![获取密钥和终结点](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组也会删除该组中包含的任何其他资源。

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。****
2. 找到包含要删除的资源的资源组
3. 右键单击资源组列表。 选择“删除资源组”并进行确认。****

## <a name="see-also"></a>请参阅

* [对 Azure 认知服务的请求进行身份验证](authentication.md)
* [什么是 Azure 认知服务？](Welcome.md)
* [自然语言支持](language-support.md)
* [Docker 容器支持](cognitive-services-container-support.md)
