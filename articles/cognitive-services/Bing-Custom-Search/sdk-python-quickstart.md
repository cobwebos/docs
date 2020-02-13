---
title: 快速入门：使用 Python SDK 调用必应自定义搜索终结点
titleSuffix: Azure Cognitive Services
description: 参考本快速入门开始使用 Python SDK 从必应自定义搜索实例请求搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: d67075fad719b1780682c705f0e17f15c5801559
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136154"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>快速入门：使用 Python SDK 调用必应自定义搜索终结点 

参考本快速入门开始使用 Python SDK 从必应自定义搜索实例请求搜索结果 虽然必应自定义搜索具有与大多数编程语言兼容的 REST API，但必应自定义搜索 SDK 提供了一种简单方法来将服务集成到应用程序中。 在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) 上可以找到此示例的源代码，以及附加的错误处理方法和注释。

## <a name="prerequisites"></a>必备条件

- 必应自定义搜索实例。 请参阅[快速入门：创建第一个必应自定义搜索实例](quick-start.md)，了解详细信息。
- Python [2.x 或 3.x](https://www.python.org/) 
- [适用于 Python 的必应自定义搜索 SDK](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>安装 Python SDK

通过以下命令安装必应自定义搜索 SDK。

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>创建新应用程序

在你喜欢使用的编辑器或 IDE 中创建一个新的 Python 文件，然后添加以下 import 语句。

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>创建搜索客户端并发送请求

1. 为你的订阅密钥创建变量。

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-custom-endpoint'
    ```

2. 将 `CognitiveServicesCredentials` 对象与订阅密钥配合使用，创建 `CustomSearchClient` 的实例。 

    ```python
    client = CustomSearchClient(endpoint, CognitiveServicesCredentials(subscription_key))
    ```

3. 发送包含 `client.custom_instance.search()` 的搜索请求。 将搜索词追加​​到 `query` 参数，并将 `custom_config` 设置为自定义配置 ID，以便使用搜索实例。 可以从[必应自定义搜索门户](https://www.customsearch.ai/)获取 ID，只需单击“生产”选项卡即可。 

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>查看搜索结果

如果找到网页搜索结果，请获取第一个结果并输出其名称、URL 和找到的总网页数。

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [生成自定义搜索 Web 应用](./tutorials/custom-search-web-page.md)
