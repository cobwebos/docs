---
title: 快速入门：使用 Python 提取名片数据 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将通过 Python 使用表单识别器 REST API，以从英文名片图像中提取数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 5e27aaebc015f47e0fcdb5da81770d49b86ad000
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88934321"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>快速入门：通过 Python 使用表单识别器 REST API 以提取名片数据

在本快速入门中，你将通过 Python 使用 Azure 表单识别器 REST API，以提取和识别名片中的相关信息。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，必须具备以下条件：
- 安装 [Python](https://www.python.org/downloads/)（若要在本地运行此示例）。
- 名片图像。 在本快速入门中，可以使用[示例图像](../media/business-card-english.jpg)。

> [!NOTE]
> 此快速入门使用本地文件。 若要使用由 URL 访问的远程名片图像，请参阅[参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>分析名片

若要开始分析名片，请使用以下 Python 脚本调用[分析名片](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) API。 在运行该脚本之前，请进行以下更改：

1. 将 `<endpoint>` 替换为从表单识别器订阅中获取的终结点。
1. 将 `<path to your business card>` 替换为名片图像或 PDF 的本地路径。
1. 将 `<subscription key>` 替换为从上一步复制的订阅密钥。
1. 将 `<file type>` 替换为“image/jpeg”、“image/png”、“application/pdf”或“image/tiff”。

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. 将代码保存在以 .py 为扩展名的文件中。 例如 form-recognizer-businesscards.py.
1. 打开命令提示符窗口。
1. 在提示符处，使用 `python` 命令运行示例。 例如，`python form-recognizer-businesscards.py`。

你将收到 `202 (Success)` 响应，其中包括 **Operation-Location** 标头，脚本会将其输出到控制台。 此标头包含一个可用于查询长时间运行的操作状态和获取结果的结果 ID。 在以下示例值中，`operations/` 后面的字符串就是结果 ID。

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>获取名片结果

调用分析名片 API 后，请调用[获取分析名片结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeBusinessCardResult) API，以获取操作状态和已提取的数据 。 将以下代码添加到 Python 脚本的底部。 这将在新的 API 调用中使用结果 ID 值。 此脚本定期调用 API，直到结果可用为止。 我们建议调用间隔为一秒或更长时间。

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. 保存脚本。
1. 再次使用 `python` 命令运行示例。 例如，`python form-recognizer-businesscards.py`。

### <a name="examine-the-response"></a>检查响应
![Contoso 公司的名片](../media/business-card-english.jpg)

此示例说明表单识别器返回的 JSON 输出。 为了提高可读性，此响应已被截断。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

脚本会向控制台输出响应，直到“分析名片”操作完成。 `"readResults"` 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。 `"documentResults"` 节点包含模型发现的特定于名片的值。 在此，你可以找到有用的联系人信息，如公司名、名字、姓氏、电话号码等。


## <a name="next-steps"></a>后续步骤

在本快速入门中，你通过 Python 使用表单识别器 REST API，提取了名片的内容。 接下来，请参阅参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 参考文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
