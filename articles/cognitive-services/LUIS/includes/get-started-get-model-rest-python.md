---
title: 使用 C# 通过 REST 调用获取模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: a21834cec456ded82c15dc916cc4991a196ea22c
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966951"
---
## <a name="prerequisites"></a>必备条件

* Azure 语言理解 - 创作资源 32 字符密钥和创作终结点 URL。 使用 [Azure 门户](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)或 [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli) 创建。
* 从 cognitive-services-language-understanding GitHub 存储库中导入 [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) 应用。
* 导入的 TravelAgent 应用的 LUIS 应用程序 ID。 应用程序仪表板中显示了应用程序 ID。
* 接收言语的应用程序中的版本 ID。 默认 ID 为“0.1”。
* [Python 3.6](https://www.python.org/downloads/) 或更高版本。
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>以编程方式更改模型

使用 Go 将经过机器学习的实体 [API](https://aka.ms/luis-apim-v3-authoring) 添加到应用程序中。

1. 创建名为 `model.py` 的新文件。 添加以下代码：

    ```python
    ########### Python 3.6 #############
    import requests

    # 32 character Authoring key
    LUIS_authoringKey  = "YOUR-KEY"

    LUIS_APP_ID = "YOUR-APP-ID"

    # Authoring endpoint, example: your-resource-name.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"

    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/train'

    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}

    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())

    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())

    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())

    addUtterances()
    train()
    trainStatus()
    ```

1. 将以 `YOUR-` 开头的值替换为你自己的值。

    |信息|目的|
    |--|--|
    |`YOUR-KEY`|32 字符创作密钥。|
    |`YOUR-ENDPOINT`| 创作 URL 终结点。 例如，`replace-with-your-resource-name.api.cognitive.microsoft.com` 。 在创建资源时设置资源名称。|
    |`YOUR-APP-ID`| LUIS 应用 ID。 |

    分配的密钥和资源可以在 LUIS 门户的“Azure 资源”  页上的“管理”部分中看到。 应用 ID 可以在“应用程序设置”  页的同一“管理”部分中找到。

1. 在创建该文件的同一目录中，在命令提示符下输入以下命令来运行文件：

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，请从文件系统中删除该文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的最佳实践](../luis-concept-best-practices.md)
