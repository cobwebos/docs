---
title: 必应自定义搜索 Python 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253047"
---
开始使用适用于 Python 的必应自定义搜索客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。 借助必应自定义搜索 API，可为关注的主题创建定制的无广告搜索体验。此示例的源代码可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) 上找到。

使用适用于 Python 的必应自定义搜索客户端库，可以执行以下操作：
* 使用必应自定义搜索实例在网上查找搜索结果。

[参考文档](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [包 (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [示例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>先决条件

- 必应自定义搜索实例。 请参阅[快速入门：创建第一个必应自定义搜索实例](../../quick-start.md)，了解详细信息。
- Python [2.x 或 3.x](https://www.python.org/) 
- [适用于 Python 的必应自定义搜索 SDK](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>安装 Python 客户端库

使用以下命令安装必应自定义搜索客户端库。

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

1. 为你的订阅密钥和终结点创建变量。

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. 将 `CognitiveServicesCredentials` 对象与订阅密钥配合使用，创建 `CustomSearchClient` 的实例。 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
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
> [生成自定义搜索 Web 应用](../../tutorials/custom-search-web-page.md)
