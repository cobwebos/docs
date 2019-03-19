---
title: 在映像中-人脸 API 检测人脸
titleSuffix: Azure Cognitive Services
description: 了解如何使用返回的人脸检测功能的各种数据。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588765"
---
# <a name="get-face-detection-data"></a>获取人脸检测数据

本指南将演示如何使用人脸检测从给定的图像中提取性别、 年龄或姿势之类的属性。 本指南中的代码片段用C#使用人脸 API 客户端库，但相同的功能是可通过[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

本指南将介绍如何为：

- 获取图像中的位置和尺寸的人脸。
- 获取各种人脸特征点 （瞳孔、 鼻子、 嘴巴，等） 在图像中的位置。
- 猜出性别、 年龄和情感，以及检测到的人脸的其他特性。

## <a name="setup"></a>设置

本指南假定你已构造**[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 对象，名为`faceClient`，使用人脸订阅密钥和终结点 URL。 在这里中,，您可以使用人脸检测功能通过调用**[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** （本指南中使用） 或**[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. 请参阅[检测人脸的快速入门C#](../quickstarts/csharp-detect-sdk.md)有关如何对此进行设置的说明。

本指南将重点介绍检测调用的具体情况&mdash;可以传递哪些参数和返回的数据可以执行的操作。 我们建议为每个操作需要额外的时间才能完成仅查询所需的功能。

## <a name="get-basic-face-data"></a>获取基本的人脸数据

若要查找人脸并获取其位置在图像中的，调用方法替换_returnFaceId_参数设置为**true** （默认值）。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

返回**[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** 对象可查询其唯一的 Id 和一个矩形，从而使人脸的像素坐标。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

请参阅**[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** 如何解析的位置和人脸的维度的信息。 通常情况下，此矩形包含眼睛、 眉毛、 鼻子和嘴巴;不一定包含的头节点、 耳朵和下巴顶部。 如果你想要使用人脸矩形裁剪完整的头部或中景肖像 （照片身份证类型的图像），可能想要通过在每个方向的边距将矩形展开。

## <a name="get-face-landmarks"></a>获取人脸特征点

人脸特征点是一组易于查找如瞳孔人脸上的点或鼻子的提示。 可以通过设置获取人脸地标数据_returnFaceLandmarks_参数**true**。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

默认有 27 个预定义的特征点。 下图显示了所有 27 个点：

![包含所有 27 个特征点标有的人脸关系图](../Images/landmarks.1.jpg)

返回的点是以像素为单位，就像人脸矩形框一样的单位。 下面的代码演示如何可能会检索鼻子和瞳孔的位置：

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

此外可以使用人脸特征点数据以准确地计算人脸的方向。 例如，我们可以定义为一个从嘴巴中心到眼睛中心的向量的人脸的旋转。 下面的代码将计算此向量：

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

如果知道所面临的方向，然后可以旋转矩形人脸框架更正确对齐。 如果你想要裁剪图像中人脸，可以以编程方式旋转图像，以使人脸始终显示竖直的情况下。

## <a name="get-face-attributes"></a>获取人脸属性

除了人脸矩形和特征点，人脸检测 API 可以分析人脸的几个概念的特性。 其中包括：

- Age
- 性别
- 笑容程度
- 面部毛发
- 眼镜
- 3D 头部姿势
- 情感

> [!IMPORTANT]
> 这些属性通过使用统计算法预测，并且始终可能不准确。 根据属性数据做出决策时要格外小心。
>

若要分析人脸属性，设置_returnFaceAttributes_参数的列表**[组 FaceAttributeType 枚举](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 值。

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

然后，获取对返回的数据的引用，并执行进一步操作根据你的需要。

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

若要了解有关每个属性的详细信息，请参阅[术语表](../Glossary.md)。

## <a name="next-steps"></a>后续步骤

本指南介绍了如何使用人脸检测的各种功能。 接下来，请参阅[术语表](../Glossary.md)有关已检索到的人脸数据的更多详细信息。

## <a name="related-topics"></a>相关主题

- [参考文档 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [参考文档 (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
