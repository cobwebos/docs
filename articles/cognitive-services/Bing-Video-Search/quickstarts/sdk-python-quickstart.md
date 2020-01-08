---
title: 快速入门：使用适用于 Python 的 SDK 搜索视频 - 必应视频搜索
titleSuffix: Azure Cognitive Services
description: 通过本快速入门使用适用于 Python 的必应视频搜索 SDK 来发送视频搜索请求
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: c1afea4e6cacc1f6e9ac84ca20e638836540396e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448392"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>快速入门：使用适用于 Python 的必应视频搜索 SDK 执行视频搜索

通过本快速入门开始使用适用于 Python 的必应视频搜索 SDK 来搜索新闻。 虽然必应视频搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 此示例的源代码可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) 上找到，其中包含其他注释和功能。

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>必备条件

- [Python](https://www.python.org/) 2.x 或 3.x
- 适用于 Python 的必应视频搜索 SDK

建议使用 Python [虚拟环境](https://docs.python.org/3/tutorial/venv.html)。 可以使用 [venv 模块](https://pypi.python.org/pypi/virtualenv)安装并初始化虚拟环境。 通过以下命令安装适用于 Python 2.7 的 virtualenv：

```console
python -m venv mytestenv
```

通过以下命令安装必应视频搜索 SDK：

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建新的 Python 文件，然后添加以下 import 语句。 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. 为你的订阅密钥创建变量。 

    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>创建搜索客户端

创建 `CognitiveServicesCredentials` 的一个实例并实例化客户端：

```python
client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
```

## <a name="send-a-search-request-and-get-a-response"></a>发送搜索请求并获取响应

1. 将 `client.videos.search()` 与搜索查询配合使用，向必应视频搜索 API 发送请求并获取响应。

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. 如果响应包含搜索结果，请获取第一个结果，输出其 ID、名称和 URL。

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另请参阅 

- [什么是必应视频搜索 API？](../overview.md)
- [认知服务 .NET SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
