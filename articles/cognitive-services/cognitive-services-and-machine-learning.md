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
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531473"
---
# <a name="cognitive-services-and-machine-learning"></a>认知服务和机器学习

认知服务提供机器学习功能，以解决一般问题，例如分析情绪中的文本或分析图像以识别对象或面孔。 您不需要特殊的机器学习或数据科学知识来使用这些服务。 

[认知服务](welcome.md)是一组服务，每个服务都支持不同的广义预测功能。 这些服务分为不同的类别，以帮助您找到合适的服务。 

|服务类别|目的|
|--|--|
|[决定](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|构建应用，用于呈现有助于做出明智和高效决策的建议。|
|[语言](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|让应用能够通过预建的脚本处理自然语言、评估情绪，并了解如何识别用户需求。|
|[搜索](https://azure.microsoft.com/services/cognitive-services/directory/search/)|将必应搜索 API 添加到应用，并利用该功能通过单个 API 调用实现数十亿网页、图像、视频和新闻的梳理。|
|[语音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|将语音转换为文本，将文本转换为自然语音。 从一种语言翻译成另一种语言，并启用说话人验证和识别。|
|[影像](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|识别和确定你的图片、视频和数字墨迹内容，为它们添加描述文字和编制索引，并审查这些内容。|
||||

在您：

* 可以使用通用解决方案。
* 从编程 REST API 或 SDK 访问解决方案。 

在您：

* 需要选择算法，并需要对非常具体的数据进行培训。

## <a name="what-is-machine-learning"></a>什么是机器学习？

机器学习是一个概念，将数据和算法结合在一起，以解决特定需求。 训练数据和算法后，输出是一个模型，您可以对不同的数据再次使用。 经过培训的模型根据新数据提供见解。 

构建机器学习系统的过程需要一些机器学习或数据科学知识。

机器学习是使用[Azure 机器学习 （AML） 产品和服务](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)提供的。

## <a name="what-is-a-cognitive-service"></a>什么是认知服务？

认知服务提供机器学习解决方案中的全部或部分组件：数据、算法和经过训练的模型。 这些服务旨在要求了解您的数据，而无需机器学习或数据科学经验。 这些服务同时提供 REST API 和基于语言的 SDK。 因此，您需要具备编程语言知识才能使用服务。

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>认知服务和 Azure 机器学习 （AML） 如何相似？

两者都有应用人工智能 （AI） 来增强业务运营的最终目标是，尽管每种产品在相应产品中提供此功能的方式是不同的。 

通常，受众不同：

* 认知服务适用于没有机器学习经验的开发人员。
* Azure 机器学习专为数据科学家量身定制。 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>认知服务与机器学习有什么不同？

认知服务为您提供经过培训的模型。 这将数据和算法汇集在一起，可从 REST API 或 SDK 获得。 您可以在几分钟内实现此服务，具体取决于您的方案。  认知服务提供一般问题的答案，如文本中的关键短语或图像中的项目标识。 

机器学习是一个通常需要较长时间才能成功实现的过程。 这一时间用于数据收集、清理、转换、算法选择、模型培训和部署，以达到认知服务提供的相同级别的功能。 通过机器学习，可以提供高度专业化和/或特定问题的答案。 机器学习问题需要熟悉所考虑问题的特定主题和数据，以及数据科学的专业知识。

## <a name="what-kind-of-data-do-you-have"></a>你有什么数据？

认知服务作为一组服务，可能需要训练模型的一些、某些或所有自定义数据。 

### <a name="no-additional-training-data-required"></a>无需额外的培训数据

提供经过全面培训的模型的服务可以被视为_黑匣子_。 您不需要知道他们是如何工作的，也不需要知道哪些数据用于训练它们。 您将数据带到经过全面训练的模型中，以获得预测。 

### <a name="some-or-all-training-data-required"></a>所需的部分或全部培训数据

某些服务允许您携带自己的数据，然后训练模型。 这允许您使用服务的数据和算法将模型与您自己的数据扩展。 输出符合您的需求。 当您自带数据时，您可能需要以特定于服务的方式标记数据。 例如，如果要训练模型以标识花，则可以提供花图像目录以及每个图像中花的位置来训练模型。 

服务可能_允许您_提供数据以增强其自己的数据。 服务_可能需要您_提供数据。 

### <a name="real-time-or-near-real-time-data-required"></a>需要实时或接近实时数据

服务可能需要实时或接近实时数据来构建有效的模型。 这些服务处理大量模型数据。 

## <a name="service-requirements-for-the-data-model"></a>数据模型的服务要求

以下数据按允许或需要的数据类型对每个服务进行分类。

|认知服务|无需培训数据|您提供部分或全部培训数据|实时或近实时数据收集|
|--|--|--|--|
|[异常检测器](./Anomaly-Detector/overview.md)|x|x|x|
|必应搜索 |x|||
|[计算机视觉](./Computer-vision/Home.md)|x|||
|[内容审阅者](./Content-Moderator/overview.md)|x||x|
|[自定义愿景](./Custom-Vision-Service/home.md)||x||
|[脸](./Face/Overview.md)|x|x||
|[表单识别器](./form-recognizer/overview.md)||x||
|[沉浸式阅读器](./immersive-reader/overview.md)|x|||
|[墨迹识别器](./Ink-recognizer/overview.md)|x|x||
|[语言理解（LUIS）](./LUIS/what-is-luis.md)||x||
|[个性化体验创建服务](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[扬声器识别器](./speaker-recognition/home.md)||x||
|[语音文本到语音转换 （TTS）](speech-service/text-to-speech.md)|x|x||
|[语音到文本 （STT）](speech-service/speech-to-text.md)|x|x||
|[语音翻译](speech-service/speech-translation.md)|x|||
|[文本分析](./text-analytics/overview.md)|x|||
|[Translator 文本](./translator/translator-info-overview.md)|x|||
|[翻译文本 - 自定义翻译器](./translator/custom-translator/overview.md)||x||

*个性化服务只需要由服务收集的培训数据（因为它实时运行）来评估您的政策和数据。 个性化程序不需要大型历史数据集进行前期或批处理培训。 

## <a name="where-can-you-use-cognitive-services"></a>在哪里可以使用认知服务？
 
这些服务用于任何可以进行 REST API 或 SDK 调用的应用程序。 应用程序的示例包括网站、机器人、虚拟或混合现实、桌面和移动应用程序。 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure 认知搜索与认知服务的关系如何？

[Azure 认知搜索](../search/search-what-is-azure-search.md)是一个单独的云搜索服务，可以选择使用认知服务将图像和自然语言处理添加到索引工作负荷。 认知服务通过包装单个 API[的内置技能](../search/cognitive-search-predefined-skills.md)在 Azure 认知搜索中公开。 您可以将免费资源用于演练，但计划为较大的卷创建和附加[计费资源](../search/cognitive-search-attach-cognitive-services.md)。

## <a name="how-can-you-use-cognitive-services"></a>如何使用认知服务？

每个服务都提供有关您的数据的信息。 您可以将服务组合到链式解决方案中，例如将语音（音频）转换为文本、将文本转换为多种语言，然后使用翻译的语言从知识库中获取答案。 虽然认知服务可以自行创建智能解决方案，但它们也可以与传统机器学习项目结合使用，以补充模型或加快开发过程。 

为其他机器学习工具提供导出模型的认知服务：

|认知服务|模型信息|
|--|--|
|[自定义愿景](./custom-vision-service/home.md)|[导出](./Custom-Vision-Service/export-model-python.md)用于 Android 的 Tensorflow，iOS11 的 CoreML，用于 Windows ML 的 ONNX|

## <a name="learn-more"></a>了解详细信息

* [架构指南 - 微软的机器学习产品是什么？](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [机器学习 - 深度学习与机器学习简介](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>后续步骤

* 在[Azure 门户](cognitive-services-apis-create-account.md)或使用[Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)创建认知服务帐户。
* 了解如何对认知服务[进行身份验证](authentication.md)。
* 使用[诊断日志记录](diagnostic-logging.md)进行问题识别和调试。 
* 在 Docker[容器](cognitive-services-container-support.md)中部署认知服务。
* 随时了解[最新的服务更新](https://azure.microsoft.com/updates/?product=cognitive-services)。
