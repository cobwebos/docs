---
title: 如何指定识别模型-人脸
titleSuffix: Azure Cognitive Services
description: 本文介绍如何选择要用于 Azure 面部应用程序的识别模式。
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.openlocfilehash: 44392b807659ff8f13511b48d0afd33db080e4f6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166464"
---
# <a name="specify-a-face-recognition-model"></a>指定人脸识别模型

本指南演示如何使用 Azure 人脸服务为人脸检测、标识和相似性搜索指定人脸识别模型。

人脸服务使用机器学习模型对图像中的人脸执行操作。 我们会根据客户反馈和研究的进步，继续改进模型的准确性，并将这些改进作为模型更新提供。 开发人员可以选择指定要使用的面部识别模型的哪个版本;他们可以选择最适合用例的模型。

如果你是新用户，建议使用最新的模型。 继续阅读以了解如何在不同的面部操作中指定它，同时避免模型冲突。 如果你是高级用户并且不确定是否应切换到最新模型，请跳到 "[评估不同模型](#evaluate-different-models)" 部分以评估新模型，并使用当前数据集比较结果。

## <a name="prerequisites"></a>必备组件

你应该熟悉 AI 人脸检测和识别的概念。 如果你没有，请先参阅以下操作方法指南：

* [如何检测图像中的人脸](HowtoDetectFacesinImage.md)
* [如何识别图像中的人脸](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>检测具有指定模型的人脸

人脸检测识别人脸的视觉特征点，并查找其边界框位置。 它还提取人脸的功能，并将其存储在标识中使用。 所有这些信息构成一个人脸的表示形式。

当提取面部功能时，将使用该识别模型，以便您可以在执行检测操作时指定模型版本。

使用人[脸检测]API 时，请使用 `recognitionModel` 参数分配模型版本。 可用值有：

* `recognition_01`
* `recognition_02`

还可以指定_returnRecognitionModel_参数（默认值**为 false**），以指示是否应在响应中返回_recognitionModel_ 。 因此，[脸检测]REST API 的请求 URL 如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

如果你使用的是客户端库，则可以通过传递表示版本的字符串来为 `recognitionModel` 分配值。
如果将其保留为未分配状态，将使用默认模型版本（_recognition_01_）。 请参阅下面的 .NET 客户端库代码示例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>用指定模型标识面部

人脸服务可以提取图像中的人脸数据，并将其与**Person**对象关联（例如，通过添加人[脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API 调用），多个**person**对象可以一起存储在**person group**中。 然后，可以将一个新的人脸与**person group** （带有人[脸标识]呼叫）进行比较，并识别该组内的匹配人员。

**PersonGroup** 中的所有 **Person** 应有一个唯一的识别模型，在创建该组使用 [PersonGroup - 创建] 或 [LargePersonGroup - 创建] 时，可以使用 `recognitionModel` 参数指定此模型。 如果未指定此参数，则使用原始 `recognition_01` 模型。 组将始终使用创建它时所用的识别模型，并在将其添加到此模型时，新的人脸将与此模型关联;创建组后无法更改此。 若要查看配置了**person group**的模型，请使用[PersonGroup - Get] API，并将_returnRecognitionModel_参数设置为**true**。

请参阅下面的 .NET 客户端库代码示例。

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

在此代码中，创建了一个 ID 为 `mypersongroupid` **person group** ，并将其设置为使用_recognition_02_模型来提取面部功能。

同样，需要指定在检测要与此**person group**进行比较的面部时要使用的模型（通过[脸检测]API）。 你使用的模型应始终与**person group**的配置一致;否则，操作将失败，因为模型不兼容。

人[脸标识]API 没有变化;只需在检测中指定模型版本。

## <a name="find-similar-faces-with-specified-model"></a>查找具有指定模型的类似面部

还可以指定用于相似性搜索的识别模式。 创建具有[FaceList-创建] API 或[LargeFaceList-创建]的人脸列表时，可以通过 `recognitionModel` 分配模型版本。 如果未指定此参数，则使用原始 `recognition_01` 模型。 人脸列表将始终使用创建它时所用的识别模型，并在将其添加到此模型时，新的人脸将与此模型关联;创建后无法更改此。 若要查看为其配置了人脸列表的模型，请使用[FaceList-Get] API，并将_returnRecognitionModel_参数设置为**true**。

请参阅下面的 .NET 客户端库代码示例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

此代码使用_recognition_02_的功能提取模型创建名为 `My face collection`的人脸列表。 当你在此面部列表中搜索检测到的新人脸时，必须使用_recognition_02_模型检测到（[脸检测]）该人脸。 如上一节所示，模型需要保持一致。

[面部查找类似]API 中没有变化;仅在检测中指定模型版本。

## <a name="verify-faces-with-specified-model"></a>验证具有指定模型的面部

[Face - Verify] API 会检查两张人脸是否属于同一个人。 验证 API 中没有关于识别模型的更改，但您只能比较在同一模型中检测到的人脸。 因此，两面都需要使用 `recognition_01` 或 `recognition_02`来检测。

## <a name="evaluate-different-models"></a>评估不同的模型

如果要比较数据的性能和_recognition_02_模型的_recognition_01_性能，将需要执行以下操作：

1. 分别创建具有_recognition_01_和_recognition_02_的两个**person group**。
1. 使用图像数据检测人脸并将其注册到这两个**person group**的**人**，并使用[PersonGroup - 训练] API 触发训练过程。
1. 在**person group**上用人[脸标识]测试并比较结果。

如果您通常指定置信度阈值（一个介于零之间的值和一个确定模型必须如何识别人脸的值），则可能需要对不同的模型使用不同的阈值。 一个模型的阈值不应共享到另一个模型，因此不一定会产生相同的结果。

## <a name="next-steps"></a>后续步骤

本文介绍了如何指定要用于不同面部服务 Api 的识别模式。 接下来，请遵循快速入门，开始使用人脸检测。

* [面部 .NET SDK](../Quickstarts/csharp-sdk.md)
* [面部 Python SDK](../Quickstarts/python-sdk.md)

[脸检测]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[面部查找类似]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[脸标识]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 训练]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList-创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
