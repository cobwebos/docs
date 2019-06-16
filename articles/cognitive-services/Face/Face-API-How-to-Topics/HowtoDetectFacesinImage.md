---
title: 检测图像中的人脸 - 人脸 API
titleSuffix: Azure Cognitive Services
description: 了解如何使用人脸检测功能返回的各种数据。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124550"
---
# <a name="get-face-detection-data"></a>获取人脸检测数据

本指南演示如何使用人脸检测从给定的图像中提取性别、 年龄或姿势之类的属性。 本指南中的代码片段用C#使用 Azure 认知服务人脸 API 客户端库。 相同的功能是可通过[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

本指南演示如何为：

- 获取图像中人脸的位置和维度。
- 获取各种人脸特征点，如瞳孔、 鼻子和嘴巴，图像中的位置。
- 猜出性别、 年龄、 表情和检测到的人脸的其他特性。

## <a name="setup"></a>设置

本指南假定已构造[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)对象，名为`faceClient`，使用人脸订阅密钥和终结点 URL。 在这里中,，您可以使用人脸检测功能通过调用[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)，在本指南中，用于或[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)。 有关如何设置此功能的说明，请参阅[检测人脸的快速入门C# ](../quickstarts/csharp-detect-sdk.md)。

本指南重点介绍检测调用的详细信息，如哪些参数可以传递和可以返回的数据执行的操作。 我们建议你查询所需的功能。 每次操作花费额外的时间来完成。

## <a name="get-basic-face-data"></a>获取基本人脸数据

若要查找人脸并获取其位置在图像中的，调用方法替换_returnFaceId_参数设置为**true**。 此设置为默认设置。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

您可以查询返回[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)对象为其唯一 Id 和一个矩形，它提供了将人脸的像素坐标。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

有关如何分析的位置和尺寸将人脸的信息，请参阅[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)。 通常情况下，此矩形包含眼睛、 眉毛、 鼻子和嘴巴。 头节点、 耳朵和下巴顶部不一定是包括在内。 若要使用人脸矩形裁剪完整的头部或中景肖像，可能获得的照片 ID 类型的映像，您可以展开每个方向中的矩形。

## <a name="get-face-landmarks"></a>获取人脸特征点

[人脸特征点](../concepts/face-detection.md#face-landmarks)是一组易于查找人脸，如瞳孔、 鼻子的提示上的点。 若要获取人脸地标数据，请设置_returnFaceLandmarks_参数**true**。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

以下代码演示如何检索鼻子和瞳孔的位置：

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

此外可以使用人脸特征点数据以准确地计算人脸的方向。 例如，你可以定义所面临的旋转为从嘴巴中心到眼睛中心的向量。 以下代码计算此向量：

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

当您知道所面临的方向时，可以旋转矩形人脸框架更正确对齐。 若要裁剪图像中人脸，可以以编程方式旋转图像，使人脸始终显示竖直的情况下。

## <a name="get-face-attributes"></a>获取人脸特性

除了人脸矩形和特征点以外，人脸检测 API 还可以分析人脸的几个概念特性。 有关完整列表，请参阅[人脸属性](../concepts/face-detection.md#attributes)概念部分。

若要分析人脸属性，设置_returnFaceAttributes_参数的列表[组 FaceAttributeType 枚举](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)值。

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

然后，获取对返回的数据的引用，并执行更多操作根据你的需要。

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

若要了解有关每个属性的详细信息，请参阅[面部检测和属性](../concepts/face-detection.md)概念指南。

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何使用人脸检测的各种功能。 接下来，请按深度教程的说明操作，将这些功能集成到应用中。

- [教程：创建一个用于显示图像中人脸数据的 WPF 应用](../Tutorials/FaceAPIinCSharpTutorial.md)
- [教程：创建一个用于检测和定格图像中人脸的 Android 应用](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>相关主题

- [参考文档 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [参考文档 (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)