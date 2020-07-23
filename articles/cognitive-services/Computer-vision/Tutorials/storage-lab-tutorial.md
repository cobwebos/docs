---
title: 教程：生成 Azure 图像的元数据
titleSuffix: Azure Cognitive Services
description: 本教程介绍如何将 Azure 计算机视觉服务集成到 Web 应用中，以便生成图像的元数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 43172cb08bb1e31c8cff891628ca6ef85cb8c864
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404413"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>教程：使用计算机视觉在 Azure 存储中生成图像元数据

本教程介绍如何将 Azure 计算机视觉服务集成到 Web 应用中，以便生成已上传图像的元数据。 这对于[数字资产管理 (DAM)](../Home.md#computer-vision-for-digital-asset-management) 方案非常有用，例如，如果公司希望为其所有图像快速生成描述性标题或可搜索的关键字。

GitHub 上的 [Azure 存储和认知服务实验室](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md)提供了完整的应用指南，本教程主要介绍该实验室的练习 5。 你可能想要遵循每个步骤创建完整的应用程序，但如果你只想了解如何将计算机视觉集成到现有的 Web 应用，请参阅本文。

本教程演示如何：

> [!div class="checklist"]
> * 在 Azure 中创建计算机视觉资源
> * 对 Azure 存储图像进行图像分析
> * 将元数据附加到 Azure 存储图像
> * 使用 Azure 存储资源管理器查看图像元数据

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 

## <a name="prerequisites"></a>先决条件

- [Visual Studio 2017 Community 版本](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)或更高版本，并已安装“ASP.NET 和 Web 开发”或“Azure 开发”工作负荷。
- 一个 Azure 存储帐户，其中包含为图像存储设置的 Blob 容器（如果需要此步骤的帮助，请按 [Azure 存储实验的练习 1](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) 中的说明操作）。
- Azure 存储资源管理器工具（如果需要此步骤的帮助，请按 [Azure 存储实验的练习 2](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) 中的说明操作）。
- 一个可以访问 Azure 存储的 ASP.NET Web 应用程序（如果需要快速创建这样的应用，请按 [Azure 存储实验室的练习 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) 中的说明操作）。

## <a name="create-a-computer-vision-resource"></a>创建计算机视觉资源

需要为 Azure 帐户创建计算机视觉资源；该资源管理你对 Azure 的计算机视觉服务的访问权限。 

1. 按照[创建 Azure 认知服务资源](../../cognitive-services-apis-create-account.md)中的说明创建计算机视觉资源。

1. 然后，转到你的资源组的菜单，单击刚创建的计算机视觉 API 订阅。 将“终结点”  下的 URL 复制到可以随后轻松地进行检索的某个位置。 然后，单击“显示访问密钥”。 

    ![Azure 门户页面，其中列出了终结点 URL 和访问密钥链接](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. 在下一窗口中，将“密钥 1”的值复制到剪贴板。 

    ![“管理密钥”对话框，其中列出了“复制”按钮](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>添加计算机视觉凭据

接下来，向应用添加所需凭据，以便该应用能够访问计算机视觉资源

在 Visual Studio 中打开 ASP.NET Web 应用程序，导航到项目根目录中的 **Web.config** 文件。 将以下语句添加到文件的 `<appSettings>` 节，将 `VISION_KEY` 替换为你在上一步复制的键，将 `VISION_ENDPOINT` 替换为你在此前的步骤中保存的 URL。

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

然后，在解决方案资源管理器中右键单击项目，使用“管理 NuGet 包”命令  安装 **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** 包。 该包包含调用计算机视觉 API 所需的类型。

## <a name="add-metadata-generation-code"></a>添加元数据生成代码

接下来，添加可以实际利用计算机视觉服务的代码，以便为图像创建元数据。 这些步骤将应用到实验室中的 ASP.NET 应用，但你可以根据自己应用的情况修改它们。 重要的是，目前你有了一个 ASP.NET Web 应用程序，该应用程序可以将图像上传到 Azure 存储容器，从其中读取图像，然后在视图中显示图像。 如果对此步骤不确定，最好是按照 [Azure 存储实验室的练习 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) 中的说明操作。 

1. 在项目的 **Controllers** 文件夹中打开 *HomeController.cs* 文件，将下面的 `using` 语句添加到文件顶部：

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. 然后转到 **Upload** 方法；此方法将图像转换并上传到 Blob 存储。 将以下代码直接添加到以 `// Generate a thumbnail` 开头的块后面（或者添加到 image-blob-creation 过程的末尾）。 此代码使用包含图像 (`photo`) 的 Blob，并使用计算机视觉为该图像生成说明。 计算机视觉 API 也生成一系列适用于图像的关键字。 生成的说明和关键字存储在 Blob 的元数据中，供以后检索。

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. 接下来，转到同一文件中的 **Index** 方法。 此方法枚举存储在目标 Blob 容器中的图像 Blob（采用 **IListBlobItem** 实例的形式），并将其传递到应用程序视图。 将此方法中的 `foreach` 块替换为以下代码。 此代码调用 **CloudBlockBlob.FetchAttributes**，以获取每个 Blob 的附加元数据。 它从元数据提取计算机生成的说明 (`caption`)，然后将它添加到 **BlobInfo** 对象，该对象会被传递到视图。
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>测试应用程序

在 Visual Studio 中保存所做的更改，然后按 **Ctrl+F5**，在浏览器中启动应用程序。 使用此应用上传一些图像，不管是从实验室资源的“photos”文件夹上传，还是从你自己的文件夹上传。 将光标悬停在视图中的某个图像上方时，会出现工具提示窗口，显示计算机生成的图像标题。

![计算机生成的标题](../Images/thumbnail-with-tooltip.png)

若要查看所有附加的元数据，请使用 Azure 存储资源管理器来查看用于图像的存储容器。 右键单击容器中的任意 Blob，然后选择“属性”。  在对话框中，会看到键值对的列表。 计算机生成的图像说明存储在“Caption”项中，搜索关键字存储在“Tag0”、“Tag1”之类的项中。 完成后，单击**取消**即可关闭对话框。

![“图像属性”对话框窗口，其中列出了元数据标记](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>清理资源

若要继续使用 Web 应用，请参阅 [后续步骤](#next-steps) 部分。 如果不打算继续使用此应用程序，则应删除所有特定于应用的资源。 若要删除资源，可以删除包含 Azure 存储订阅和计算机视觉资源的资源组。 这样会删除存储帐户、上传到其中的 Blob，以及与 ASP.NET Web 应用进行连接所需的应用服务资源。 

若要删除资源组，请在门户中打开“资源组”选项卡，  导航到用于此项目的资源组，然后单击视图顶部的“删除资源组”。  系统会要求你键入资源组的名称来确认你要删除它，因为资源组一旦删除就不能恢复。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何在现有的 Web 应用中设置 Azure 的计算机视觉服务，以便在 Blob 图像上传时自动为其生成标题和关键字。 接下来请参阅 Azure 存储实验室的练习 6，了解如何向 Web 应用添加搜索功能。 这样就可以利用计算机视觉服务生成的搜索关键字。

> [!div class="nextstepaction"]
> [向应用添加搜索](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
