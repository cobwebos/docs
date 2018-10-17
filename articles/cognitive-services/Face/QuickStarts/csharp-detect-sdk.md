---
title: 快速入门：检测图像中的人脸 - SDK、C#
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用认知服务中的人脸 Windows C# 客户端库检测图像中的人脸。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: nolachar
ms.openlocfilehash: a9a7efd89f8e7462812064615d07acf12acbc3a3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364077"
---
# <a name="quickstart-detect-faces-in-an-image-using-c35---face"></a>快速入门：使用 C&#35; 检测图像中的人脸 - 人脸

在本快速入门中，你将使用人脸 Windows 客户端库检测图像中的人脸。

该示例的源代码可在 [Github](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face) 上获得。

## <a name="prerequisites"></a>先决条件

* 需要一个订阅密钥来运行此示例。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。
* 任何版本的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) 客户端库 NuGet 包。 不需下载程序包。 下面提供了安装说明。

## <a name="detectwithurlasync-method"></a>DetectWithUrlAsync 方法

`DetectWithUrlAsync` 和 `DetectWithStreamAsync` 方法分别为远程图像和本地图像包装[人脸 - 检测 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。 使用这些方法可检测图像中的人脸并返回人脸属性，包括：

* 人脸 ID：多个人脸 API 方案中使用的唯一 ID。
* 人脸矩形：左侧、顶部、宽度和高度，指示人脸在图像中的位置。
* 特征点：一系列（27 个点）人脸特征点，这些特征点指向人脸组件的重要位置。
* 人脸属性包括年龄、性别、笑容程度、头部姿态和面部毛发。

若要运行此示例，请执行以下步骤：

1. 在 Visual Studio 中创建一个新的 Visual C# 控制台应用。
1. 安装人脸客户端库 NuGet 包。
    1. 在顶部菜单上，单击“工具”，然后依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包”。
    1. 单击“浏览”选项卡，然后选择“包括预发行版”。
    1. 在“搜索”框中，键入“Microsoft.Azure.CognitiveServices.Vision.Face”。
    1. 选择显示的 Microsoft.Azure.CognitiveServices.Vision.Face，单击项目名称旁边的复选框，然后单击“安装”。
1. 将 Program.cs 更换为以下代码。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 如有必要，将 `faceEndpoint` 更改为与订阅密钥关联的 Azure 区域。
1. （可选）将 <`LocalImage>` 替换为某个本地图像的路径和文件名（如果未设置将忽略）。
1. （可选）将 `remoteImageUrl` 设置为另一图像。
1. 运行该程序。

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace DetectFace
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // You must use the same region as you used to get your subscription
        // keys. For example, if you got your subscription keys from westus,
        // replace "westcentralus" with "westus".
        //
        // Free trial subscription keys are generated in the westcentralus
        // region. If you use a free trial subscription key, you shouldn't
        // need to change the region.
        // Specify the Azure region
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg";

        private static readonly FaceAttributeType[] faceAttributes =
            { FaceAttributeType.Age, FaceAttributeType.Gender };

        static void Main(string[] args)
        {
            FaceClient faceClient = new FaceClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });
            faceClient.Endpoint = faceEndpoint;

            Console.WriteLine("Faces being detected ...");
            var t1 = DetectRemoteAsync(faceClient, remoteImageUrl);
            var t2 = DetectLocalAsync(faceClient, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Detect faces in a remote image
        private static async Task DetectRemoteAsync(
            FaceClient faceClient, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine("\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            try
            {
                IList<DetectedFace> faceList =
                    await faceClient.Face.DetectWithUrlAsync(
                        imageUrl, true, false, faceAttributes);

                DisplayAttributes(GetFaceAttributes(faceList, imageUrl), imageUrl);
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imageUrl + ": " + e.Message);
            }
        }

        // Detect faces in a local image
        private static async Task DetectLocalAsync(FaceClient faceClient, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            try
            {
                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    IList<DetectedFace> faceList =
                            await faceClient.Face.DetectWithStreamAsync(
                                imageStream, true, false, faceAttributes);
                    DisplayAttributes(
                        GetFaceAttributes(faceList, imagePath), imagePath);
                }
            }
            catch (APIErrorException e)
            {
                Console.WriteLine(imagePath + ": " + e.Message);
            }
        }

        private static string GetFaceAttributes(
            IList<DetectedFace> faceList, string imagePath)
        {
            string attributes = string.Empty;

            foreach (DetectedFace face in faceList)
            {
                double? age = face.FaceAttributes.Age;
                string gender = face.FaceAttributes.Gender.ToString();
                attributes += gender + " " + age + "   ";
            }

            return attributes;
        }

        // Display the face attributes
        private static void DisplayAttributes(string attributes, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(attributes + "\n");
        }
    }
}
```

### <a name="detectwithurlasync-response"></a>DetectWithUrlAsync 响应

成功响应会显示图像中每张人脸的性别和年龄。

有关原始 JSON 输出的示例，请参阅 [API 快速入门：使用 C# 检测图像中的人脸](CSharp.md)。

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>后续步骤

了解如何创建使用人脸服务来检测图像中的人脸的 WPF Windows 应用程序。 该应用程序围绕每张脸绘制一个框，并在状态栏上显示人脸描述。

> [!div class="nextstepaction"]
> [教程：创建一个用于检测和定格图像中人脸的 WPF 应用](../Tutorials/FaceAPIinCSharpTutorial.md)
