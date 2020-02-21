---
title: 使用 C# 通过 REST 调用获取模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368465"
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

1. 创建名为 `model.py` 的新文件。 添加以下代码：

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

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
