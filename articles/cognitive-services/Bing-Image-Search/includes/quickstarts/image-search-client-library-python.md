---
title: 必应图像搜索 Python 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: fbe44602d4f586e47eada1b6043817e9d26c6793
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85805719"
---
在本快速入门中，你将使用必应图像搜索客户端库（它是 API 的包装器并包含相同的功能）进行你的第一次图像搜索。 此简单的 Python 应用程序发送图像搜索查询，分析 JSON 响应，并显示返回的第一个图像的 URL。

[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) 上提供了此示例的源代码以及附加的错误处理和注释。

## <a name="prerequisites"></a>先决条件

* [Python 2.7 或 3.4](https://www.python.org/) 以及更高版本。

* 适用于 Python 的 [Azure 图像搜索客户端库](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/)
    * 使用 `pip install azure-cognitiveservices-search-imagesearch` 进行安装

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在最喜爱的 IDE 或编辑器中创建新的 Python 脚本，然后将发生以下导入：

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. 为订阅密钥和搜索词创建变量。

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>创建图像搜索客户端

1. 创建 `CognitiveServicesCredentials` 的实例并使用它实例化客户端：

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```
1. 向必应图像搜索 API 发送搜索查询：
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>处理和查看结果

分析响应中所返回的图像结果。


如果响应中包含搜索结果，则存储第一个结果并输出其详细信息，例如缩略图 URL、原始 URL 以及返回的图像的总数。  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索单页应用教程](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>另请参阅

* [什么是必应图像搜索？](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [尝试在线互动演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure 认知服务 SDK 的 Python 示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Azure 认知服务文档](https://docs.microsoft.com/azure/cognitive-services)
* [必应图像搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
