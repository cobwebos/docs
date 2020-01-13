---
title: 快速入门：使用 Python 提取回执数据 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，将表单识别器 REST API 与 Python 结合使用，以从销售回执图像中提取数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: pafarley
ms.openlocfilehash: 5d95527fa5333b113fb53840fd687313a139413a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450919"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>快速入门：将表单识别器 REST API 与 Python 结合使用来提取回执数据

在本快速入门中，会将 Azure 表单识别器 REST API 与 Python 结合使用，以提取和识别销售回执中的相关信息。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 本快速入门使用表单识别器 v1.0 API。 如果订阅位于 `West US 2` 或 `West Europe` 区域中，则应改为遵循 v[2.0 API 快速入门](./python-train-extract.md)。

## <a name="prerequisites"></a>必备条件
若要完成本快速入门，必须具备以下条件：
- 有权访问表单识别器受限访问预览版。 若要访问预览版，请填写并提交[表单识别器访问请求](https://aka.ms/FormRecognizerRequestAccess)表单。
- 安装 [Python](https://www.python.org/downloads/)（若要在本地运行此示例）。
- 回执图像的 URL。 在本快速入门中，可以使用[示例图像](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true)。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>分析回执

若要开始分析回执，请使用以下 Python 脚本调用“分析回执”API  。 在运行该脚本之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为从表单识别器订阅中获取的终结点。
1. 将 `<your receipt URL>` 替换为回执图像的 URL 地址。
1. 将 `<subscription key>` 替换为从上一步复制的订阅密钥。

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. 将代码保存在以 .py 为扩展名的文件中。 例如，form-recognizer-receipts.py  。
1. 打开命令提示符窗口。
1. 在提示符处，使用 `python` 命令运行示例。 例如，`python form-recognizer-receipts.py` 。

你将收到 `202 (Success)` 响应，其中包括 **Operation-Location** 标头，脚本会将其输出到控制台。 此标头包含一个可用于查询操作状态和获取分析结果的操作 ID。 在以下示例值中，`operations/` 后面的字符串就是操作 ID。

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>获取回执结果

调用了“分析回执”API  后，可以调用“获取回执结果”API  ，以获取操作状态和已提取的数据。 将以下代码添加到 Python 脚本的底部。 这将提取操作 ID 值并将其传递给新的 API 调用。 该操作是异步的，因此此脚本会定期调用 API，直到结果可用为止。 我们建议调用间隔为一秒或更长时间。

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. 保存脚本。
1. 再次使用 `python` 命令运行示例。 例如，`python form-recognize-analyze.py` 。

### <a name="examine-the-response"></a>检查响应

脚本会向控制台输出响应，直到分析操作完成。 然后，它将以 JSON 格式输出已提取的文本数据。 `"recognitionResults"` 字段包含已从回执中提取的每行文本，而 `"understandingResults"` 字段包含该回执最相关部分的键/值信息。

请查看以下回执图像及其相应的 JSON 输出。 为可读起见，已将该输出缩短。

![Contoso 商店提供的回执](../media/contoso-receipt.png)

`"recognitionResults"` 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。 `"understandingResults"` 节点包含模型发现的特定于回执的值。 你将在这里找到有用的键/值对，如税款、总计、商家地址等。

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们已使用表单识别器 REST API 和 Python 训练了一个模型，并在示例案例中运行了该模型。 接下来，请参阅参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)