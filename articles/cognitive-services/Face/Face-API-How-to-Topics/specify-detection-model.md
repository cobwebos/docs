---
title: 如何指定检测模型的人脸 API
titleSuffix: Azure Cognitive Services
description: 本文将演示如何选择要使用与 Azure 人脸 API 应用程序的人脸检测模型。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: fbefbea171a8559466117c55703a14268b4c4c99
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592927"
---
# <a name="specify-a-face-detection-model"></a>指定人脸检测模型

本指南演示如何为 Azure 人脸 API 指定人脸检测模型。

人脸 API 使用机器学习模型针对图像中的人脸执行运算。 我们将会根据客户反馈以及研究成果不断改进模型的准确度，并作为模型更新交付改进结果。 开发人员可以选择指定他们希望使用; 哪个版本的人脸检测模型他们可以选择最适合其用例的模型。

继续阅读以了解如何在某些人脸操作中指定的人脸检测模型。 人脸 API 使用人脸检测，只要它将某种其他形式的数据转换为的人脸图像。

如果不确定是否应该使用最新模型，请跳到[评估不同的模型](#evaluate-different-models)部分评估新模型并使用当前数据集的结果进行比较。

## <a name="prerequisites"></a>必备组件

您应熟悉 AI 人脸检测的概念。 如果不是，请参阅的人脸检测概念指南或操作方法指南：

* [人脸检测概念](../concepts/face-detection.md)
* [如何检测图像中的人脸](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>使用指定的模型检测人脸

人脸检测查找人脸的边界框位置，并标识其 visual 特征点。 它提取人脸的功能并将其存储以便以后用于[识别](../concepts/face-recognition.md)操作。

当你使用[人脸-检测]API，可以分配模型版本具有`detectionModel`参数。 可用值为：

* `detection_01`
* `detection_02`

请求 URL[人脸-检测]REST API 将如下所示：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]
&subscription-key=<Subscription key>`

如果使用客户端库，则可以分配的值为`detectionModel`通过传入一个合适的字符串。 如果将其分配的 API 将使用默认的模型版本 (`detection_01`)。 请查看适用于 .NET 客户端库的以下代码示例。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>使用指定的模型将人脸添加到人员

人脸 API 可以从图像中提取人脸数据并将其与**Person**对象，通过[person Group 人员-添加人脸](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API。 在此 API 调用时，可以检测模型指定为在相同的方式[人脸-检测]。

请查看适用于 .NET 客户端库的以下代码示例。

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

此代码将创建**person Group** id `mypersongroupid` ，并添加**人员**到它。 然后它将人脸添加到此**Person**使用`detection_02`模型。 如果未指定*detectionModel*参数时，API 将使用的默认模型`detection_01`。

> [!NOTE]
> 无需使用相同的检测模型中的所有人脸**Person**对象，并且你无需使用相同的检测模型检测新的人脸时要与进行比较**人员**对象 (在[人脸-识别]API，例如)。

## <a name="add-face-to-facelist-with-specified-model"></a>使用指定的模型将人脸添加到 FaceList

此外可以指定检测模型时将人脸添加到现有**FaceList**对象。 请查看适用于 .NET 客户端库的以下代码示例。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

此代码将创建**FaceList**称为`My face collection`并将人脸添加到其与`detection_02`模型。 如果未指定*detectionModel*参数时，API 将使用的默认模型`detection_01`。

> [!NOTE]
> 无需使用相同的检测模型中的所有人脸**FaceList**对象，并且你无需使用相同的检测模型检测新的人脸时要与进行比较**FaceList**对象。

## <a name="evaluate-different-models"></a>评估不同的模型

针对不同的任务进行了优化不同的人脸检测模型。 请参阅下表中的差异的概述。

|**detection_01**  |**detection_02**  |
|---------|---------|
|所有人脸检测操作的默认选择。 | 发布 2019 年 5，可根据需要在所有人脸检测操作。
|未优化的小型、 端视图或模糊的人脸。  | 在小型、 端视图和模糊的人脸的更加的准确。 |
|如果它们检测调用中指定，则返回人脸属性 （头部姿势、 年龄、 表情，等）。 |  不返回人脸属性。     |
|如果它们检测调用中指定，则返回人脸特征点。   | 不返回人脸特征点。  |

要比较的性能的最佳方式`detection_01`和`detection_02`模型是在示例数据集上使用它们。 我们建议调用[人脸-检测]API 上各种不同映像，尤其是映像的很多人脸或人脸，而很难看到，使用每个检测模型。 注意每个模型返回的实际面部数。

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何指定要使用不同的人脸 Api 使用的检测模型。 接下来，请遵循相应的快速入门开始使用人脸识别。

* [图像 (.NET SDK) 中检测人脸](../quickstarts/csharp-detect-sdk.md)

[人脸-检测]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[人脸-识别]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
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