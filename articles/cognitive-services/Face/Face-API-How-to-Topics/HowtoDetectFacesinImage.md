---
title: 检测图像中的人脸
titleSuffix: Azure Cognitive Services
description: 本指南演示如何使用人脸检测从给定图像中提取性别、年龄或姿势等属性。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 7070cb3bcd1b519828a750cf4ba6caf7ecb34bbb
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169877"
---
# <a name="get-face-detection-data"></a>获取人脸检测数据

本指南演示如何使用人脸检测从给定图像中提取性别、年龄或姿势等属性。 本指南中的代码片段使用 Azure 认知C#服务面部客户端库来编写。 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)提供了相同的功能。

本指南演示如何执行以下操作：

- 获取图像中面部的位置和尺寸。
- 获取图像中各种面部特征点（如瞳孔、鼻子和嘴）的位置。
- 推测检测到的人脸的性别、年龄、情感和其他属性。

## <a name="setup"></a>设置

本指南假定你已使用人脸订阅密钥和终结点 URL 构造名为 `faceClient`的[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)对象。 从这里，你可以通过调用[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)（在本指南中使用）或[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)来使用人脸检测功能。 有关如何设置此功能的说明，请单击其中一个快速入门。

本指南重点介绍检测调用的具体信息，如可以传递的参数以及可对返回的数据执行的操作。 建议仅查询所需的功能。 每个操作需要额外的时间才能完成。

## <a name="get-basic-face-data"></a>获取基本的面部数据

若要查找人脸并获取图像中的位置，请调用方法，并将_returnFaceId_参数设置为**true**。 此设置为默认设置。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

可以查询返回的[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)对象的唯一 id，并为其提供字样的像素坐标。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

有关如何分析脸的位置和尺寸的信息，请参阅[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)。 通常，此矩形包含眼睛、眉毛、鼻子和嘴。 头部、耳和下巴向上看不一定包括在内。 若要使用人脸矩形裁剪整个标题或获取中间的纵向（也许是照片 ID 类型图像），可以在每个方向上展开该矩形。

## <a name="get-face-landmarks"></a>获取人脸特征点

人[脸特征点](../concepts/face-detection.md#face-landmarks)是人脸上的一组易于查找的点，如鼻子的瞳孔或笔尖。 若要获取面部路标数据，请将_returnFaceLandmarks_参数设置为**true**。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

以下代码演示了如何检索鼻子和瞳孔的位置：

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

还可以使用人脸特征点数据准确地计算人脸的方向。 例如，可以将表面的旋转定义为从嘴中心到眼睛中心的矢量。 下面的代码计算此向量：

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

当您知道面的方向时，您可以旋转矩形面框以更正确地对齐。 若要裁剪图像中的人脸，可以通过编程方式旋转图像，使面部始终显示为直立。

## <a name="get-face-attributes"></a>获取面部特性

除了面部矩形和特征点，人脸检测 API 还可以分析人脸的多个概念属性。 有关完整列表，请参阅[面部属性](../concepts/face-detection.md#attributes)概念部分。

若要分析人脸属性，请将_returnFaceAttributes_参数设置为[FaceAttributeType 枚举](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)值的列表。

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

然后，获取对返回的数据的引用，并根据需要执行更多操作。

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

若要了解有关每个属性的详细信息，请参阅人[脸检测和属性](../concepts/face-detection.md)概念指南。

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何使用各种面部检测功能。 接下来，按照深入的教程将这些功能集成到应用中。

- [教程：创建 WPF 应用以在图像中显示人脸数据](../Tutorials/FaceAPIinCSharpTutorial.md)
- [教程：创建 Android 应用程序以检测图像中的人脸](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>相关主题

- [参考文档（REST）](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [参考文档（.NET SDK）](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)