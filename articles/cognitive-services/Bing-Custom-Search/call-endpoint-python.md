---
title: 使用 Python 调用终结点 - 必应自定义搜索 - Microsoft 认知服务
description: 本快速入门演示如何通过使用 Python 调用必应自定义搜索终结点来从自定义搜索实例中请求搜索结果。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "35366845"
---
# <a name="call-bing-custom-search-endpoint-python"></a>调用必应自定义搜索终结点 (Python)

本快速入门演示如何通过使用 Python 调用必应自定义搜索终结点来从自定义搜索实例中请求搜索结果。 

## <a name="prerequisites"></a>先决条件
若要完成本快速入门，你需要：

- 自定义搜索实例。 请参阅[创建第一个必应自定义搜索实例](quick-start.md)。

-  安装 [Python](https://www.python.org/)。

- 具有必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)足以满足此快速入门的要求。 需要激活免费试用版时提供的访问密钥，或使用 Azure 仪表板中的付费订阅密钥。 

## <a name="run-the-code"></a>运行代码

要调用必应自定义搜索终结点，请执行以下步骤：

1. 为代码创建文件夹。
2. 从管理员命令提示符或终端处导航至刚刚创建的文件夹。
3. 安装 requests python 模块：
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. 创建 BingCustomSearch.py 文件并将向其中复制以下代码。
8. 用密钥和配置 ID（见步骤 1）替换“YOUR-SUBSCRIPTION-KEY”和“YOUR-CUSTOM-CONFIG-ID”。

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. 使用以下命令运行代码。
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>后续步骤
- [配置托管 UI 体验](./hosted-ui.md)
- [使用修饰标记来突出显示文本](./hit-highlighting.md)
- [网页](./page-webpages.md)