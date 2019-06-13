---
title: 快速入门：必应视觉搜索 SDK、Python
titleSuffix: Azure Cognitive Services
description: 设置视觉搜索 SDK Python 控制台应用程序。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 06/11/2018
ms.author: v-gedod
ms.openlocfilehash: eb115d1bfe033761c8dab82b693e4e694ff3bd2b
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734640"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-python"></a>快速入门：使用适用于 Python 的必应视觉搜索 SDK 获取图像见解

根据本快速入门，开始使用 Python SDK 通过必应视觉搜索服务获取图像见解。 虽然必应视觉搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 上找到此示例的源代码 

## <a name="prerequisites"></a>先决条件

* [Python](https://www.python.org/) 2.x 或 3.x
* 建议使用[虚拟环境](https://docs.python.org/3/tutorial/venv.html)。 使用 [venv 模块](https://pypi.python.org/pypi/virtualenv)安装并初始化虚拟环境。 安装适用于 Python 2.7 的 virtualenv。
* 适用于 Python 的必应视觉搜索 SDK。 通过使用以下命令来安装它：
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`



[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建新的 Python 文件，然后添加以下 import 语句。 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. 针对订阅密钥、自定义配置 ID 和要上传的图像创建变量。 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. 对客户端进行实例化

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>发送搜索请求

1. 打开图像文件，将 `VisualSearchRequest()` 串行化，并将其传递为 `visual_search()` 的 `knowledge_request` 参数。

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. 如果返回了任何结果，则打印它们和标记以及第一个标记中的操作。

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建单页 Web 应用](tutorial-bing-visual-search-single-page-app.md)
