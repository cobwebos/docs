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
ms.openlocfilehash: f5b524ca6156dab7c0d1e38ad320b721f40a49ef
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169760"
---
# <a name="specify-a-face-detection-model"></a>指定人脸检测模型

本指南说明如何为 Azure 面部服务指定人脸检测模型。

人脸服务使用机器学习模型对图像中的人脸执行操作。 我们会根据客户反馈和研究的进步，继续改进模型的准确性，并将这些改进作为模型更新提供。 开发人员可以选择指定要使用的面部检测模型的版本;他们可以选择最适合用例的模型。

继续阅读以了解如何在某些面部运营中指定面部检测模型。 面部服务在将人脸转换为某种其他形式的数据时使用人脸检测。

如果您不确定是否应该使用最新模型，请跳到 "[评估不同模型](#evaluate-different-models)" 部分以评估新模型，并使用当前数据集比较结果。

## <a name="prerequisites"></a>必备组件

你应熟悉 AI 面部检测的概念。 如果你没有，请参阅面部检测概念指南或操作方法指南：

* [人脸检测概念](../concepts/face-detection.md)
* [如何检测图像中的人脸](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>检测具有指定模型的人脸

人脸检测查找人脸的边界箱位置并标识其视觉对象特征点。 它提取人脸的功能，并将其存储起来供以后在[识别](../concepts/face-recognition.md)操作中使用。

使用人[脸检测]API 时，可以使用 `detectionModel` 参数分配模型版本。 可用值有：

* `detection_01`
* `detection_02`

人[脸检测]REST API 的请求 URL 如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

如果你使用的是客户端库，则可以通过传入适当的字符串来为 `detectionModel` 分配值。 如果将其保留为未分配状态，则 API 将使用默认模型版本（`detection_01`）。 请参阅下面的 .NET 客户端库代码示例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>将人脸添加到指定的模型

人脸服务可以提取图像中的人脸数据，并通过[Person group 人员-添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API 将其与**person**对象相关联。 在此 API 调用中，可以采用与人[脸检测]中相同的方式来指定检测模型。

请参阅下面的 .NET 客户端库代码示例。

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

此代码将创建 `mypersongroupid` ID 的**person group** ，并向其中添加**人员**。 然后，使用 `detection_02` 模型向此**人添加人**脸。 如果未指定*detectionModel*参数，则 API 将使用默认模型 `detection_01`。

> [!NOTE]
> 您无需为 Person 对象中的所有**人**都使用同一检测模型，并且在检测新面部以与 Person 对象进行比较时无需使用同一检测模型（例如，在**人**[脸识别]API 中）。

## <a name="add-face-to-facelist-with-specified-model"></a>用指定的模型将人脸添加到 FaceList

您还可以在将人脸添加到现有的**FaceList**对象时指定检测模型。 请参阅下面的 .NET 客户端库代码示例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

此代码将创建一个名为 `My face collection` 的**FaceList** ，并将其添加到 `detection_02` 模型中。 如果未指定*detectionModel*参数，则 API 将使用默认模型 `detection_01`。

> [!NOTE]
> 不需要对**FaceList**对象中的所有人都使用同一检测模型，并且在检测新面部时不需要使用同一检测模型来与**FaceList**对象进行比较。

## <a name="evaluate-different-models"></a>评估不同的模型

不同的面部检测模型针对不同的任务进行了优化。 有关差异的概述，请参阅下表。

|**detection_01**  |**detection_02**  |
|---------|---------|
|所有面部检测操作的默认选项。 | 在2019年5月发布，并可根据需要在所有面部检测操作中使用。
|不针对小型、侧视图或模糊面部进行优化。  | 提高了对小型、侧视图和模糊面的准确性。 |
|如果在检测调用中指定了人脸属性（head 姿势、年龄、情感等），则返回这些属性。 |  不返回面部特性。     |
|如果在检测调用中指定了面部特征点，则返回。   | 不返回人脸特征点。  |

比较 `detection_01` 和 `detection_02` 模型的性能的最佳方式是在示例数据集上使用它们。 建议在各种图像上调用人[脸检测]API，尤其是在使用每个检测模型时很难查看的许多面或面部图像。 注意每个模型返回的面部数。

## <a name="next-steps"></a>后续步骤

本文介绍了如何指定要用于不同人脸 Api 的检测模型。 接下来，请遵循快速入门，开始使用人脸检测。

* [面部 .NET SDK](../Quickstarts/csharp-sdk.md)
* [面部 Python SDK](../Quickstarts/python-sdk.md)

[脸检测]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[脸识别]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
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
