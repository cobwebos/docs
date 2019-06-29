---
title: 如何指定识别模型 - 人脸 API
titleSuffix: Azure Cognitive Services
description: 本文介绍如何选择要对 Azure 人脸 API 应用程序使用的识别模型。
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: e8d5c416183a7d475a46c5e538577069612baf8e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449004"
---
# <a name="specify-a-face-recognition-model"></a>指定人脸识别模型

本指南介绍如何指定识别模型，以使用 Azure 人脸 API 进行人脸检测、识别和相似性搜索。

人脸 API 使用机器学习模型针对图像中的人脸执行运算。 我们将会根据客户反馈以及研究成果不断改进模型的准确度，并作为模型更新交付改进结果。 开发人员可以选择指定想要使用的人脸识别模型版本；他们可以选择最适合其用例的模型。

对于新用户，我们建议使用最新的模型。 请继续阅读，了解如何在不同的人脸操作中指定模型，同时避免模型冲突。 如果高级用户不确定是否要切换到最新的模型，可以转到[评估不同的模型](#evaluate-different-models)部分来评估新模型，并使用当前数据集比较结果。

## <a name="prerequisites"></a>必备组件

读者应该熟悉 AI 人脸检测和识别的概念。 否则请先阅读以下操作指南：

* [如何检测图像中的人脸](HowtoDetectFacesinImage.md)
* [如何识别图像中的人脸](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>使用指定的模型检测人脸

人脸检测可以识别人脸的视觉特征点，并查找其边界框位置。 它还能提取人脸的特征，并将其存储以在识别时使用。 所有这些信息构成了人脸的表示形式。

提取人脸特征时将使用识别模型，因此，可以在执行检测操作时指定模型版本。

使用 [Face - Detect] API 时，可以使用 `recognitionModel` 参数分配模型版本。 可用值为：

* `recognition_01`
* `recognition_02`

或者，可以指定 _returnRecognitionModel_ 参数（默认为 **false**）来指示是否应在响应中返回 _recognitionModel_。 因此，[Face - Detect] REST API 的请求 URL 将如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

如果使用客户端库，则可以通过传递一个表示版本的字符串来分配 `recognitionModel` 的值。
如果不分配该值，将使用默认模型版本 (_recognition_01_)。 请查看适用于 .NET 客户端库的以下代码示例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>使用指定的模型识别人脸

人脸 API 可以从图像中提取人脸数据，并将其与 **Person** 对象相关联（例如，通过 [Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 调用），多个 **Person** 对象可以一起存储在 **PersonGroup** 中。 然后，可以根据 **PersonGroup** 比较新的人脸（使用 [Face - Identify] 调用），该组中匹配的人员将被识别。

**PersonGroup** 中的所有 **Person** 应有一个唯一的识别模型，在创建该组（使用 [PersonGroup - 创建] 或 [LargePersonGroup - 创建]）时，可以使用 `recognitionModel` 参数指定此模型。 如果不指定此参数，则会使用原始的 `recognition_01` 模型。 某个组始终使用创建它时所用的识别模型，将新的人脸添加到该组时，它们将与此模型相关联；创建组后无法更改此行为。 若要查看 **PersonGroup** 是使用哪个模型配置的，请结合设置为 **true** 的 _returnRecognitionModel_ 参数使用 [PersonGroup - Get] API。

请查看适用于 .NET 客户端库的以下代码示例。

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

在此代码中，创建了 ID 为 `mypersongroupid` 的 **PersonGroup**，它设置为使用 _recognition_02_ 模型提取人脸特征。

相应地，需要指定在检测人脸以根据此 **PersonGroup** 进行比较（通过[Face - Detect] API）时要使用的模型。 使用的模型应始终为 **PersonGroup** 的配置相一致；否则，操作将会由于模型不兼容而失败。

无需在 [Face - Identify] API 中进行更改；只需在检测中指定模型版本。

## <a name="find-similar-faces-with-specified-model"></a>使用指定的模型查找类似的人脸

还可以指定一个识别模型来进行类似性搜索。 可以在使用 [FaceList - Create] API 或 [LargeFaceList - Create] 创建人脸列表时，使用 `recognitionModel` 来分配模型版本。 如果不指定此参数，则会使用原始的 `recognition_01` 模型。 某个人脸列表始终使用创建它时所用的识别模型，将新的人脸添加到该组时，它们将与此模型相关联；创建后无法更改此行为。 若要查看人脸列表是使用哪个模型配置的，请结合设置为 **true** 的 _returnRecognitionModel_ 参数使用 [FaceList - Get] API。

请查看适用于 .NET 客户端库的以下代码示例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

此代码使用用于提取特征的 _recognition_02_ 模型创建名为 `My face collection` 的人脸列表。 在此人脸列表中搜索与新检测到的人脸类似的人脸时，该人脸必须已使用 _recognition_02_ 模型检测到 ([Face - Detect])。 如上一部分所述，模型需要保持一致。

无需在 [Face - Find Similar] API 中进行更改；只需在检测中指定模型版本。

## <a name="verify-faces-with-specified-model"></a>使用指定的模型验证人脸

[Face - Verify] API 会检查两张人脸是否属于同一个人。 无需在 Verify API 中进行识别模型方面的更改，但只能比较使用同一模型检测到的人脸。 因此，两张人脸都需要是使用 `recognition_01` 或 `recognition_02` 检测到的。

## <a name="evaluate-different-models"></a>评估不同的模型

如果基于数据比较 _recognition_01_ 和 _recognition_02_ 模型的性能，需要：

1. 分别使用 _recognition_01_ 和 _recognition_02_ 创建两个 **PersonGroup**。
1. 使用图像数据检测人脸，并将其注册到两个 **PersonGroup** 的 **Person**，然后使用 [PersonGroup - 训练] API 触发训练过程。
1. 在两个 **PersonGroup** 中使用 [Face - Identify] 进行测试，然后比较结果。

如果正常指定了置信度阈值（介于 0 与 1 之间的值，确定模型必须有多大的置信度来识别人脸），可能需要对不同的模型使用不同的阈值。 一个模型的阈值不能与另一个模型共享，并且不一定生成相同的结果。

## <a name="next-steps"></a>后续步骤

本文已介绍如何指定要与不同人脸服务 API 配合使用的识别模型。 接下来，请遵循相应的快速入门开始使用人脸识别。

* [检测图像中的人脸](../quickstarts/csharp-detect-sdk.md)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a（人脸 - 验证）
[PersonGroup - 创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 训练]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
