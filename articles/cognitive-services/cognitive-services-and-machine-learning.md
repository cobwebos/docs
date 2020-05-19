---
title: 认知服务和机器学习
titleSuffix: Azure Cognitive Services
description: 了解在哪些情况下适合将 Azure 认知服务与其他用于机器学习的 Azure 产品/服务配合使用。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: ec997c802f83b0d1eea54c0710b0ebc4684397fe
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584614"
---
# <a name="cognitive-services-and-machine-learning"></a>认知服务和机器学习

认知服务提供机器学习功能来解决一般问题，例如分析激动人心的情绪的文本或分析图像以识别对象或面部。 若要使用这些服务，无需特殊的机器学习或数据科学知识。 

[认知服务](welcome.md)是一组服务，每个服务支持不同的通用化预测功能。 服务分为不同的类别，以帮助你找到正确的服务。 

|服务类别|目标|
|--|--|
|[决策](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|构建应用，用于呈现有助于做出明智和高效决策的建议。|
|[语言](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|让应用能够通过预建的脚本处理自然语言、评估情绪，并了解如何识别用户需求。|
|[搜索](https://azure.microsoft.com/services/cognitive-services/directory/search/)|将必应搜索 API 添加到应用，并利用该功能通过单个 API 调用实现数十亿网页、图像、视频和新闻的梳理。|
|[语音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|将语音转换为文本，将文本转换为自然语音。 从一种语言翻译成另一种语言，并启用说话人验证和识别。|
|[影像](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|识别和确定你的图片、视频和数字墨迹内容，为它们添加描述文字和编制索引，并审查这些内容。|
||||

在以下情况时使用认知服务：

* 可以使用通用化解决方案。
* 从编程 REST API 或 SDK 访问解决方案。 

当你执行以下操作时，请使用其他机器学习解决方案：

* 需要选择算法，并需要对非常具体的数据进行训练。

## <a name="what-is-machine-learning"></a>什么是机器学习？

机器学习是一种概念，你可以将数据与算法组合在一起，以解决特定需求。 在对数据和算法进行定型后，输出就是一种可以使用不同数据再次使用的模型。 训练的模型基于新数据提供见解。 

构建机器学习系统的过程需要了解机器学习或数据科学方面的知识。

机器学习是使用[Azure 机器学习（AML）产品和服务](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)提供的。

## <a name="what-is-a-cognitive-service"></a>什么是认知服务？

认知服务提供机器学习解决方案中的部分或全部组件：数据、算法和训练模型。 这些服务旨在要求对数据的一般知识，而不需要机器学习或数据科学方面的经验。 这些服务同时提供了 REST API 和基于语言的 Sdk。 因此，您需要具有编程语言知识才能使用服务。

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>认知服务和 Azure 机器学习（AML）的相似之处是什么？

这两者都具有应用人工智能（AI）以增强业务运营的最终目标，不过，每种方法在各自的产品中提供这种操作都是不同的。 

通常，受众不同：

* 认知服务适用于没有机器学习经验的开发人员。
* Azure 机器学习针对数据科学家进行定制。 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>认知服务与机器学习有何不同？

认知服务为您提供了一个训练的模型。 这会将数据和算法一起提供，可从 REST API 或 SDK 获得。 你可以在几分钟内实现此服务，具体取决于你的方案。  认知服务提供一般问题的答案，例如图像中的文本或项标识中的关键短语。 

机器学习是一种通常需要较长时间才能成功实现的进程。 此时间花费在数据收集、清理、转换、算法选择、模型定型和部署上，以实现认知服务提供的相同级别的功能。 通过机器学习，可以提供非常专用和/或特定问题的答案。 机器学习问题需要熟悉相关问题的特定主题和数据，以及数据科学方面的专业知识。

## <a name="what-kind-of-data-do-you-have"></a>您拥有哪种类型的数据？

认知服务是一组服务，可以对定型模型要求无、部分或全部自定义数据。 

### <a name="no-additional-training-data-required"></a>无需其他培训数据

可将提供完全训练的模型的服务视为一个_黑色框_。 您无需知道它们的工作方式或用于对它们进行定型的数据。 您可以将数据引入经过完全训练的模型，以获得预测。 

### <a name="some-or-all-training-data-required"></a>需要的部分或全部定型数据

某些服务允许您引入自己的数据，然后训练模型。 这样，你就可以使用服务的数据和算法通过自己的数据来扩展模型。 输出满足你的需求。 导入自己的数据时，可能需要以特定于服务的方式标记数据。 例如，如果您正在训练一个模型来识别鲜花，则可以提供一种使用图像的目录以及每个图像中的花朵位置，以对模型进行定型。 

_利用_服务，你可以提供数据来增强其自身的数据。 服务可能_要求_你提供数据。 

### <a name="real-time-or-near-real-time-data-required"></a>需要实时或近乎实时的数据

服务可能需要实时或近乎实时的时间数据来构建有效的模型。 这些服务处理大量模型数据。 

## <a name="service-requirements-for-the-data-model"></a>数据模型的服务要求

以下数据根据服务的允许或需要的数据类型对每个服务进行分类。

|认知服务|无需定型数据|提供了部分或全部定型数据|实时或近乎实时的数据收集|
|--|--|--|--|
|[异常检测器](./Anomaly-Detector/overview.md)|x|x|x|
|必应搜索 |x|||
|[计算机视觉](./Computer-vision/Home.md)|x|||
|[内容审查器](./Content-Moderator/overview.md)|x||x|
|[自定义视觉](./Custom-Vision-Service/home.md)||x||
|[人脸](./Face/Overview.md)|x|x||
|[表单识别器](./form-recognizer/overview.md)||x||
|[沉浸式阅读器](./immersive-reader/overview.md)|x|||
|[墨迹识别器](./Ink-recognizer/overview.md)|x|x||
|[语言理解 (LUIS)](./LUIS/what-is-luis.md)||x||
|[个性化体验创建服务](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[发言人识别器](./speaker-recognition/home.md)||x||
|[语音文本到语音转换（TTS）](speech-service/text-to-speech.md)|x|x||
|[语音转换到文本（STT）](speech-service/speech-to-text.md)|x|x||
|[语音翻译](speech-service/speech-translation.md)|x|||
|[文本分析](./text-analytics/overview.md)|x|||
|[翻译工具](./translator/translator-info-overview.md)|x|||
|[翻译人员-自定义转换器](./translator/custom-translator/overview.md)||x||

* Personalizer 只需要服务收集的定型数据（它在实时运行时）来评估策略和数据。 Personalizer 不需要较大的历史数据集来进行前期或批处理培训。 

## <a name="where-can-you-use-cognitive-services"></a>在哪里可以使用认知服务？
 
服务用于可进行 REST API 或 SDK 调用的任何应用程序。 应用程序的示例包括网站、bot、虚拟或混合现实、桌面和移动应用程序。 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure 认知搜索与认知服务之间的关系是什么？

[Azure 认知搜索](../search/search-what-is-azure-search.md)是一种单独的云搜索服务，可选择使用认知服务将图像和自然语言处理添加到索引工作负荷。 认知服务在 Azure 认知搜索中通过包装单个 Api 的[内置技能](../search/cognitive-search-predefined-skills.md)公开。 您可以使用免费资源来执行演练，但计划为更大的卷创建和附加[收费资源](../search/cognitive-search-attach-cognitive-services.md)。

## <a name="how-can-you-use-cognitive-services"></a>如何使用认知服务？

每个服务都提供数据的相关信息。 可以将服务组合在一起，以将语音（音频）转换为文本，将文本翻译成多种语言，然后使用翻译后的语言从知识库获取答案。 虽然认知服务可用于自行创建智能解决方案，但也可以将其与传统的机器学习项目结合使用来补充模型或加速开发过程。 

为其他机器学习工具提供导出模型的认知服务：

|认知服务|模型信息|
|--|--|
|[自定义视觉](./custom-vision-service/home.md)|适用于 Android 的 Tensorflow[导出](./Custom-Vision-Service/export-model-python.md)，CoreML for IOS11，ONNX FOR Windows ML|

## <a name="learn-more"></a>了解详细信息

* [体系结构指南-Microsoft 的机器学习产品有哪些？](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [机器学习-深度学习和机器学习简介](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>后续步骤

* 在[Azure 门户](cognitive-services-apis-create-account.md)或[Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)中创建认知服务帐户。
* 了解如何向认知服务[进行身份验证](authentication.md)。
* 使用[诊断日志记录](diagnostic-logging.md)进行问题识别和调试。 
* 在 Docker[容器](cognitive-services-container-support.md)中部署认知服务。
* 随时了解[服务更新](https://azure.microsoft.com/updates/?product=cognitive-services)。
