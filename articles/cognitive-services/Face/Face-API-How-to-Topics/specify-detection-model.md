---
title: 如何指定检测模型-人脸
titleSuffix: Azure Cognitive Services
description: 本文介绍如何选择要与 Azure 面部应用程序一起使用的面部检测模型。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 40ca1dbf981c5a9025cf5a0bac6b007709d69a77
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "76934578"
---
# <a name="specify-a-face-detection-model"></a>指定人脸检测模型

本指南说明如何为 Azure 面部服务指定人脸检测模型。

人脸服务使用机器学习模型对图像中的人脸执行操作。 我们将会根据客户反馈以及研究成果不断改进模型的准确度，并作为模型更新交付改进结果。 开发人员可以选择指定想要使用的人脸检测模型版本；他们可以选择最适合其用例的模型。

请继续了解如何在特定的人脸操作中指定人脸检测模型。 面部服务在将人脸转换为某种其他形式的数据时使用人脸检测。

如果你不确定是否要切换到最新的模型，请跳转到[评估不同的模型](#evaluate-different-models)部分来评估新模型，并使用当前数据集比较结果。

## <a name="prerequisites"></a>必备条件

你应该熟悉 AI 人脸检测的概念。 如果你不熟悉，请参阅人脸检测的概念指南或操作指南：

* [人脸检测的概念](../concepts/face-detection.md)
* [如何检测图像中的人脸](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>使用指定的模型检测人脸

人脸检测可以查找人脸的边界框位置，并识别其视觉特征点。 它会提取人脸的特征并存储这些特征，以便稍后在[识别](../concepts/face-recognition.md)操作中使用。

使用 [Face - Detect] API 时，可以使用 `detectionModel` 参数分配模型版本。 可用值有：

* `detection_01`
* `detection_02`

[Face - Detect] REST API 的请求 URL 将如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

如果使用客户端库，则可以通过传入一个相应的字符串来分配 `detectionModel` 的值。 如果不分配该值，API 将使用默认模型版本 (`detection_01`)。 请查看适用于 .NET 客户端库的以下代码示例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>使用指定的模型将人脸添加到 Person

人脸服务可以提取图像中的人脸数据，并通过[Person group 人员-添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API 将其与**person**对象相关联。 在此 API 调用中，可以像在 [Face - Detect] 中一样指定检测模型。

请查看适用于 .NET 客户端库的以下代码示例。

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

此代码将创建 ID 为 `mypersongroupid` 的 **PersonGroup**，并将一个 **Person** 对象添加到其中。 然后，它使用 `detection_02` 模型将一个 Face 对象添加到此 **Person** 对象。 如果未指定 *detectionModel* 参数，API 将使用默认模型 `detection_01`。

> [!NOTE]
> 无需对 **Person** 对象中的所有人脸使用相同的检测模型，并且在检测新人脸时，无需使用相同的检测模型来与 **Person** 对象进行比较（例如，在 [Face - Identify] API 中）。

## <a name="add-face-to-facelist-with-specified-model"></a>使用指定的模型将人脸添加到 FaceList

将人脸添加到现有的 **FaceList** 对象时，也可以指定检测模型。 请查看适用于 .NET 客户端库的以下代码示例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

此代码将创建名为 `My face collection` 的 **FaceList**，并使用 `detection_02` 模型将一个 Face 对象添加到其中。 如果未指定 *detectionModel* 参数，API 将使用默认模型 `detection_01`。

> [!NOTE]
> 无需对 **FaceList** 对象中的所有人脸使用相同的检测模型，并且在检测新人脸时，无需使用相同的检测模型来与 **FaceList** 对象进行比较

## <a name="evaluate-different-models"></a>评估不同的模型

不同的人脸检测模型已针对不同的任务进行优化。 有关差异概述，请参阅下表。

|**detection_01**  |**detection_02**  |
|---------|---------|
|所有人脸检测操作的默认选项。 | 已于 2019 年 5 月发布，可以选择性地在所有人脸检测操作中使用。
|未针对小尺寸人脸、侧视人脸或模糊人脸进行优化。  | 已针对小尺寸人脸、侧视人脸或模糊人脸提高了准确度。 |
|如果在检测调用中指定了人脸属性（头部姿势、年龄、表情等），则返回这些属性。 |  不返回人脸属性。     |
|如果在检测调用中指定了人脸特征点，则返回这些特征点。   | 不返回人脸特征点。  |

比较 `detection_01` 和 `detection_02` 模型性能的最佳方法是针对示例数据集使用它们。 我们建议使用每个检测模型针对各种图像（尤其是许多人脸的图像，或者难以辨认的人脸的图像）调用 [Face - Detect] API。 请注意每个模型返回的人脸数。

## <a name="next-steps"></a>后续步骤

本文已介绍如何指定要与不同人脸 API 配合使用的检测模型。 接下来，请遵循相应的快速入门开始使用人脸识别。

* [面部 .NET SDK](../Quickstarts/csharp-sdk.md)
* [面部 Python SDK](../Quickstarts/python-sdk.md)
* [面部转向 SDK](../Quickstarts/go-sdk.md)

[人脸 - 检测]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[人脸 - 标识]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
