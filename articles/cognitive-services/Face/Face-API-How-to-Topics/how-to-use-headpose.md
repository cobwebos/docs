---
title: 使用 HeadPose 属性
titleSuffix: Azure Cognitive Services
description: 了解如何使用 HeadPose 属性在视频源中自动旋转人脸矩形或检测头部姿势。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 534846044770d66ec5171ad4f61de921d2d5d194
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169792"
---
# <a name="use-the-headpose-attribute"></a>使用 HeadPose 属性

本指南介绍如何使用检测到的人脸的 HeadPose 属性来启用某些重要方案。

## <a name="rotate-the-face-rectangle"></a>旋转人脸矩形

随每个检测到的人脸返回的人脸矩形用于标记人脸在图像中的位置和大小。 默认情况下，此矩形始终与图像（其边分为垂直边和水平边）对齐；在给有一定角度的人脸配框时，这可能导致效率不高。 在需要以编程方式对图像中的人脸进行裁剪的情况下，最好是能够旋转要裁剪的矩形。

[认知服务人脸 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 示例应用使用 HeadPose 属性来旋转其检测到的人脸矩形。

### <a name="explore-the-sample-code"></a>探索示例代码

可使用 HeadPose 属性以编程方式旋转人脸矩形。 如果在检测人脸（请参阅[如何检测人脸](HowtoDetectFacesinImage.md)）时指定此属性，可以稍后对其进行查询。 [认知服务人脸 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 应用中的以下方法采用一系列 **DetectedFace** 对象，返回一系列 **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** 对象。 此处的 **Face** 是一个自定义类，用于存储人脸数据，包括更新的矩形坐标。 将会针对 **top**、**left**、**width** 和 **height** 计算新值，而新字段 **FaceAngle** 用于指定旋转。

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>显示更新的矩形

在这里，可以在显示中使用返回的 **Face** 对象。 [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) 中的以下行显示如果根据该数据呈现新矩形：

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>检测头部姿势

可以通过实时跟踪 HeadPose 变化检测头部姿势，例如点头和摇头。 可以将此功能用作自定义活体检测器。

活体检测用于确定某个对象是真人，而不是图像或视频表示形式。 可以使用头部姿势检测器来验证某个对象是活体，而不是某个人的图像表示形式。

> [!CAUTION]
> 若要实时检测头部姿势，需高频率（高于每秒一次）调用人脸 API。 如果你使用的是免费层 (f0) 订阅，则无法使用此功能。 如果你使用的是付费层订阅，请确保已计算进行快速 API 调用来检测头部姿势的成本。

如需头部姿势检测的工作示例，请查看 GitHub 上的[人脸 HeadPose 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample)。

## <a name="next-steps"></a>后续步骤

如需旋转的人脸矩形的工作示例，请查看 GitHub 上的[认知服务人脸 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 应用。 也可查看[人脸 HeadPose 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples)应用，了解如何通过实时跟踪 HeadPose 属性来检测头部动作。