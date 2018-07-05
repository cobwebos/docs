---
title: 必应视觉搜索 SDK 裁剪区域结果教程 | Microsoft Docs
description: 如何使用必应视觉搜索 SDK 获取类似于上传图像裁剪区域的图像的 URL。
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 9bc3c180f108025f442343d8c5356982a83826a6
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958397"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>教程：必应视觉搜索 SDK 图像剪切区域和结果
视觉搜索 SDK 提供一个选项用于选择图像的区域，以及在线查找类似于大型图像裁剪区域的图像。  本示例指定的裁剪区域显示包含多个人像的某张图像中的一个人像。  代码将发送大型图像的裁剪区域和 URL，并返回包含必应搜索 URL 以及以联机方式找到的类似图像的 URL 的结果。

## <a name="prerequisites"></a>先决条件

需要安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 才能在 Windows 上运行此代码。 （免费的社区版也可以。）

必须创建一个具有必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)足以满足本快速入门的要求。 需要激活免费试用版时提供的访问密钥，或使用 Azure 仪表板中的付费订阅密钥。

## <a name="application-dependencies"></a>应用程序依赖项
若要使用必应 Web 搜索 SDK 设置控制台应用程序，请浏览到 Visual Studio 中的解决方案资源管理器中的“管理 NuGet 程序包”选项。 添加 Microsoft.Azure.CognitiveServices.Search.VisualSearch 包。

安装 NuGet Web 搜索 SDK 程序包还将安装依赖项，包括：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>图像和裁剪区域
下图显示 Microsoft 高级领导团队。  我们使用视觉搜索 SDK 上传图像的某个裁剪区域，并在大型图像的选定区域中查找包含该实体的其他图像和网页。  在本例中，该实体是一个人像。

![Microsoft 高级领导团队](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>在 VisualSearchRequest 中将裁剪区域指定为 ImageInfo
本示例使用前一图像的某个裁剪区域，该区域根据整张图像的百分分指定了左上和右下坐标。  以下代码从该裁剪区域创建 `ImageInfo` 对象，并将 `ImageInfo` 对象加载到 `VisualSearchRequest` 中。  `ImageInfo` 对象还包含在线图像的 URL。

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>搜索类似于裁剪区域的图像
`VisualSearchRequest` 包含有关图像及其 URL 的裁剪区域信息。  `VisualSearchMethodAsync` 方法获取结果。
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>从 ImageModuleAction 获取 URL 数据
视觉搜索结果为 `ImageTag` 对象。  每个标记都包含 `ImageAction` 对象的列表。  每个 `ImageAction` 均包含一个 `Data` 字段，它是依赖于操作类型的值的列表：

可以使用以下代码获取以前的类型：
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
完整的应用程序返回：

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

如上面的列表中所示，`Entity` `ActionType` 包含一个必应搜索查询，该查询返回有关某个可识别人像、地点或事物的信息。  `TopicResults` 和 `ImageResults` 类型包含相关图像的查询。 列表中的 URL 链接到必应搜索结果。


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>通过视觉搜索找到的图像的 PagesIncluding ActionType URL

获取实际图像 URL 需要将 `ActionType` 读取为 `ImageModuleAction` 的强制转换，其中包含具有值的列表的 `Data` 元素。  每个值是图像的 URL。  以下代码将 `PagesIncluding` 操作类型强制转换为 `ImageModuleAction` 并读取这些值。
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="complete-code"></a>完整代码

下面的代码运行前面的示例。 此代码在发布请求的正文中发送图像二进制文件和 cropArea 对象，并列显每个 ActionType 的必应搜索 API。 如果 ActionType 为 PagesIncluding，则此代码会获取 ImageObject 数据中的 ImageObject 项。  数据中包含值的列表，它们是 Web 页面上的图像的 URL。  将生成的视觉搜索 URL 复制并粘贴到浏览器，以显示结果。 将 ContentUrl 项复制并粘贴到浏览器，以显示图像。

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }

                        }

                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```
## <a name="next-steps"></a>后续步骤
[视觉搜索响应](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview#the-response)