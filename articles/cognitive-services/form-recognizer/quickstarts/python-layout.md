---
title: 快速入门：使用 Python 提取文本和布局信息 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，我们将使用表单识别器布局 REST API 和 Python 从表单中读取文本和表数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: f0c1e9eccda8171ab816d33dac3f1947cea67eea
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714604"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>快速入门：使用表单识别器 REST API 和 Python 提取文本和布局信息

在本快速入门中，我们会将 Azure 表单识别器 REST API 与 Python 结合使用，以便从表单文档中提取文本布局信息和表数据。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，必须具备以下条件：
- 安装 [Python](https://www.python.org/downloads/)（若要在本地运行此示例）。
- 表单文档。 可以从本快速入门的[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)中下载图像。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>分析表单布局

若要开始分析布局，请使用以下 Python 脚本调用 **[分析布局](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** API。 在运行该脚本之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为从表单识别器订阅中获取的终结点。
1. 将 `<path to your form>` 替换为本地表单文档的路径。
1. 将 `<subscription key>` 替换为从上一步复制的订阅密钥。

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. 将代码保存在以 .py 为扩展名的文件中。 例如，*form-recognizer-layout.py*。
1. 打开命令提示符窗口。
1. 在提示符处，使用 `python` 命令运行示例。 例如，`python form-recognizer-layout.py` 。

你将收到 `202 (Success)` 响应，其中包括 **Operation-Location** 标头，脚本会将其输出到控制台。 此标头包含一个可用于查询异步操作状态和获取结果的操作 ID。 在以下示例值中，`operations/` 后面的字符串就是操作 ID。

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>获取布局结果

调用了“分析布局”API  后，可以调用 **[获取分析布局结果](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** API，以获取操作状态和已提取的数据。 将以下代码添加到 Python 脚本的底部。 此代码在新的 API 调用中使用操作 ID 值。 此脚本定期调用 API，直到结果可用为止。 我们建议调用间隔为一秒或更长时间。

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
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
1. 再次使用 `python` 命令运行示例。 例如，`python form-recognizer-layout.py` 。

### <a name="examine-the-response"></a>检查响应

脚本会向控制台输出响应，直到“分析布局”  操作完成。 然后，它将以 JSON 格式输出已提取的数据。 `"readResults"` 节点包含每一行文本，以及其各自在页面上的边界框位置。 `"pageResults"` 字段显示表中的每个文本段，均带有行-列坐标。

请查看以下发票图像及其相应的 JSON 输出。 为了简单起见，已缩短输出。

> [!div class="mx-imgBorder"]
> ![带有表的 Contoso 发票票据](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你结合使用了表单识别器 REST API 与 Python，以提取发票的文本布局。 接下来，请参阅参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
