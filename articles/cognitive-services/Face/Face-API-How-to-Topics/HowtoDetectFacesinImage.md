---
title: 使用人脸 API 检测图像中的人脸 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 使用认知服务中的人脸 API 检测图像中的人脸。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 57cd0915450428399fd680638aa4fae2cdbe17c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365510"
---
# <a name="how-to-detect-faces-in-image"></a>如何检测图像中的人脸

本指南展示了如何通过提取性别、年龄或姿态等人脸属性来检测图像中的人脸。 示例是在 C# 中使用人脸 API 客户端库编写而成。 

## <a name="concepts"></a> 概念

如果不熟悉本指南中的以下任何概念，请随时参阅[术语表](../Glossary.md)中的定义： 

- 人脸检测
- 人脸特征点
- 头部姿态
- 人脸属性

## <a name="preparation"></a> 准备工作

本示例将展示以下功能： 

- 检测图像中的人脸，并使用矩形框架标记人脸
- 分析瞳孔、鼻子或嘴的位置，然后在图像中标记它们
- 分析人脸的头部姿态、性别和年龄

必须准备至少有一张清晰人脸的图像，才能执行这些功能。 

## <a name="step1"></a> 第 1 步：授权 API 调用

每次调用人脸 API 都需要订阅密钥。 此密钥必须通过查询字符串参数传递，或在请求头中指定。 若要通过查询字符串传递订阅密钥，请参阅[“人脸 - 检测”](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)的请求 URL 作为示例：

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

作为备选方法，也可以在 HTTP 请求头中指定订阅密钥：ocp-apim-subscription-key：&lt;订阅密钥&gt;。使用客户端库时，订阅密钥是通过 FaceServiceClient 类的构造函数传入。 例如：
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step2"></a> 第 2 步：将图像上传到服务并执行人脸检测

执行人脸检测的最基本方法是直接上传图像。 为此，请发送“POST”请求，其中包含 application/octet-stream 内容类型和从 JPEG 图像中读取的数据。 图像的大小上限为 4MB。

使用客户端库时，若要通过上传图像来执行人脸检测，请传入 Stream 对象。 请参阅以下示例：
```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

请注意，FaceServiceClient 的 DetectAsync 方法是异步的。 也应将调用方法标记为异步，才能使用 await 子句。
如果图像已发布在 Web 上且有 URL，还可以通过提供 URL 来执行人脸检测。 在本示例中，请求正文为包含 URL 的 JSON 字符串。
使用客户端库时，若要通过提供 URL 来执行人脸检测，可以使用 DetectAsync 方法的另一个重载来轻松执行。
```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

与检测到的人脸一起返回的 FaceRectangle 属性是人脸上的基本位置（以像素为单位）。 此矩形通常包含眼睛、眉毛、鼻子和嘴，不包含头顶、耳朵和下巴。 如果裁剪完整头部或中景肖像（照片 ID 类型为图像），建议扩展矩形人脸框架区域，因为人脸区域可能对某些应用来说太小。 若要更精确地定位人脸，请使用下一部分中介绍的人脸特征点（定位人脸特征或计算人脸方向机制），经证明非常有用。

## <a name="step3"></a> 第 3 步：了解并使用人脸特征点

人脸特征点是人脸上的一系列具体特征点；通常是瞳孔、眼角或鼻子等人脸组成部分特征点。 人脸特征点是可在人脸检测期间分析的可选属性。 可以在调用[“人脸 - 检测”](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)时将“true”作为布尔值传递给 returnFaceLandmarks 查询参数，也可以对 FaceServiceClient 类 DetectAsync 方法使用可选参数 returnFaceLandmarks，以便在检测结果中添加人脸特征点。

默认有 27 个预定义的特征点。 下图展示了所有 27 个特征点的定义方式：

![HowToDetectFace](../Images/landmarks.1.jpg)

与人脸矩形框架一样，返回的特征点以像素为单位。 因此，更容易标记图像中的具体兴趣点。 下面的代码展示了如何检索鼻子和瞳孔的位置：
```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

除了用于在图像中标记人脸特征之外，人脸特征点还可用于精确计算人脸方向。 例如，可以将人脸方向定义为，自嘴中心到眼睛中心的矢量。 下面的代码详细展示了人脸方向计算：

```CSharp
var landmarks = face.FaceLandmarks;
 
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

通过确定人脸方向，可以旋转矩形人脸框架，让它与人脸对齐。 很显然，人脸特征点可以提高详细程度和实用性。

## <a name="step4"></a> 第 4 步：使用其他人脸属性

除了人脸特征点外，“人脸 - 检测”API 还可以分析人脸上的其他几个属性。 这些属性包括：

- Age
- 性别
- 笑容程度
- 面部毛发
- 3D 头部姿态

这些属性是使用统计算法进行预测，不一定完全精确。 不过，仍可以按这些属性来分类人脸。 若要详细了解每个属性，请参阅[术语表](../Glossary.md)。

下面的简单示例展示了如何在人脸检测期间提取人脸属性：
```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 
## <a name="summary"></a> 总结

本指南介绍了[“人脸 - 检测”](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API 的功能：如何在本地上传的图像或 Web 上的图像 URL 中检测人脸；如何通过返回矩形人脸框架来检测人脸；以及如何分析人脸特征点、3D 头部姿态和其他人脸属性。

若要深入了解 API 的详细信息，请参阅[“人脸 - 检测”](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API 参考指南。

## <a name="related"></a> 相关主题

[如何识别图像中的人脸](HowtoIdentifyFacesinImage.md)
