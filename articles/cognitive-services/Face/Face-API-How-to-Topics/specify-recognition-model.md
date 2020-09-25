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
ms.custom: devx-track-csharp
ms.openlocfilehash: d250fc005c5760a3eecc2793d02b6f2a9161e663
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283031"
---
# <a name="specify-a-face-recognition-model"></a>指定人脸识别模型

本指南演示如何使用 Azure 人脸服务为人脸检测、标识和相似性搜索指定人脸识别模型。

人脸服务使用机器学习模型对图像中的人脸执行操作。 我们将会根据客户反馈以及研究成果不断改进模型的准确度，并作为模型更新交付改进结果。 开发人员可以选择指定想要使用的人脸识别模型版本；他们可以选择最适合其用例的模型。

Azure 面部服务具有三个可用的识别模式。  (发布的 2017) 和_recognition_02_ (发布的 2019) 的模型_recognition_01_持续支持，以确保使用通过这些模型创建的 FaceLists 或**person group**的客户能够向后兼容。 **FaceList**或**person group**将始终使用创建它时所用的识别模型，添加新的面部时，它们将与此模型关联。 此功能在创建后不能更改，客户将需要使用相应的识别模型和相应的 **FaceList** 或 **person group**。

您可以根据自己的需要移动到更高版本的识别模型;但是，需要创建新的 FaceLists 和 Persongroup 现，并将其用于所选的识别模式。

 (发布的 2020) _recognition_03_ 模型是当前可用的最准确的模型。 如果你是新客户，建议使用此模型。 _Recognition_03_ 将为相似性比较和人员匹配比较提供改进的准确性。 请注意，每个模型都独立于其他模型进行操作，并且为一个模型设置的置信度阈值不应与其他识别模型进行比较。

继续阅读以了解如何在不同的面部操作中指定所选模型，同时避免模型冲突。 如果你是高级用户，并且想要确定是否应切换到最新模型，请跳到 " [评估不同模型](#evaluate-different-models) " 部分以评估新模型，并使用当前数据集比较结果。


## <a name="prerequisites"></a>先决条件

读者应该熟悉 AI 人脸检测和识别的概念。 如果你没有，请先参阅以下指南：

* [人脸检测的概念](../concepts/face-detection.md)
* [人脸识别的概念](../concepts/face-recognition.md)
* [如何检测图像中的人脸](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>使用指定的模型检测人脸

人脸检测可以识别人脸的视觉特征点，并查找其边界框位置。 它还能提取人脸的特征，并将其存储以在识别时使用。 所有这些信息构成了人脸的表示形式。

提取人脸特征时将使用识别模型，因此，可以在执行检测操作时指定模型版本。

使用 [Face - Detect] API 时，可以使用 `recognitionModel` 参数分配模型版本。 可用值有：
* recognition_01
* recognition_02
* recognition_03


或者，可以指定 _returnRecognitionModel_ 参数（默认为 **false**）来指示是否应在响应中返回 _recognitionModel_。 因此，[Face - Detect] REST API 的请求 URL 将如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

如果使用客户端库，则可以通过传递一个表示版本的字符串来分配 `recognitionModel` 的值。 如果将其保留为未分配状态，将使用默认的模型版本 `recognition_01` 。 请查看适用于 .NET 客户端库的以下代码示例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>使用指定的模型识别人脸

人脸服务可以提取图像中的人脸数据，并将其与通过[添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API 调用 (的**person**对象关联起来，例如) ，多个**person**对象可以一起存储在**person group**中。 然后，可以根据 **PersonGroup** 比较新的人脸（使用 [Face - Identify] 调用），该组中匹配的人员将被识别。

**PersonGroup** 中的所有 **Person** 应有一个唯一的识别模型，在创建该组（使用 [PersonGroup - Create] 或 [LargePersonGroup - Create]）时，可以使用 `recognitionModel` 参数指定此模型。 如果不指定此参数，则会使用原始的 `recognition_01` 模型。 某个组始终使用创建它时所用的识别模型，将新的人脸添加到该组时，它们将与此模型相关联；创建组后无法更改此行为。 若要查看 **PersonGroup** 是使用哪个模型配置的，请结合设置为 **true** 的 _returnRecognitionModel_ 参数使用 [PersonGroup - Get] API。

请查看适用于 .NET 客户端库的以下代码示例。

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

在此代码中，创建了 ID 为 `mypersongroupid` 的 **PersonGroup**，它设置为使用 _recognition_02_ 模型提取人脸特征。

相应地，需要指定在检测人脸以根据此 **PersonGroup** 进行比较（通过[人脸 - 检测] API）时要使用的模型。 使用的模型应始终为 **PersonGroup** 的配置相一致；否则，操作将会由于模型不兼容而失败。

无需在 [Face - Identify] API 中进行更改；只需在检测中指定模型版本。

## <a name="find-similar-faces-with-specified-model"></a>使用指定的模型查找类似的人脸

还可以指定一个识别模型来进行类似性搜索。 可以在使用 [FaceList - Create] API 或 [LargeFaceList - Create] 创建人脸列表时，使用 `recognitionModel` 来分配模型版本。 如果未指定此参数，则 `recognition_01` 默认情况下使用模型。 人脸列表将始终使用创建它时所用的识别模型，并在将其添加到列表中时，新的人脸将与此模型相关联。创建后不能对其进行更改。 若要查看人脸列表是使用哪个模型配置的，请结合设置为 **true** 的 _returnRecognitionModel_ 参数使用 [FaceList - Get] API。

请查看适用于 .NET 客户端库的以下代码示例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");
```

此代码创建一个名为的人脸列表 `My face collection` ，并使用 _recognition_03_ 模型进行功能提取。 当你在此面部列表中搜索检测到的新人脸的面部列表时，必须使用_recognition_03_模型检测到 ([面部检测]) 中的人脸。 如上一部分所述，模型需要保持一致。

无需在 [Face - Find Similar] API 中进行更改；只需在检测中指定模型版本。

## <a name="verify-faces-with-specified-model"></a>使用指定的模型验证人脸

[Face - Verify] API 会检查两张人脸是否属于同一个人。 无需在 Verify API 中进行识别模型方面的更改，但只能比较使用同一模型检测到的人脸。

## <a name="evaluate-different-models"></a>评估不同的模型

如果要将不同识别模型的性能与您自己的数据进行比较，则需要：
1. 分别使用 _recognition_01_、 _recognition_02_和 _recognition_03_ 创建三个 persongroup 现。
1. 使用图像数据检测人脸并将其注册到这三个**person group**中的**用户**。 
1. 使用 Person group API 训练你的 Persongroup 现。
1. 在所有三个 **person group**上测试人脸识别并比较结果。


如果正常指定了置信度阈值（介于 0 与 1 之间的值，确定模型必须有多大的置信度来识别人脸），可能需要对不同的模型使用不同的阈值。 一个模型的阈值不能与另一个模型共享，并且不一定生成相同的结果。

## <a name="next-steps"></a>后续步骤

本文已介绍如何指定要与不同人脸服务 API 配合使用的识别模型。 接下来，请遵循相应的快速入门开始使用人脸识别。

* [面部 .NET SDK](../Quickstarts/csharp-sdk.md)
* [面部 Python SDK](../Quickstarts/python-sdk.md)
* [面部转向 SDK](../Quickstarts/go-sdk.md)

[人脸 - 检测]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[人脸 - 查找相似人脸]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[人脸 - 标识]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a（人脸 - 验证）
[PersonGroup - 创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 创建]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
