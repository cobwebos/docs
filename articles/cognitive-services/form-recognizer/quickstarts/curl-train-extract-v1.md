---
title: 快速入门：使用 cURL 训练模型并提取表单数据 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用表单识别器 REST API 和 cURL 来训练模型，并从表单中提取数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/11/2019
ms.author: pafarley
ms.openlocfilehash: 97e7341fa48bbd381bb193dcb1436e32564bb591
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451039"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>快速入门：使用 REST API 和 cURL 训练表单识别器模型并提取表单数据

在本快速入门中，我们将使用 Azure 表单识别器的 REST API 和 cURL 来训练表单并为其评分，以提取键值对和表。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 本快速入门使用表单识别器 v1.0 API。 如果订阅位于 `West US 2` 或 `West Europe` 区域中，则应改为遵循 v[2.0 API 快速入门](./curl-train-extract.md)。

## <a name="prerequisites"></a>必备条件
若要完成本快速入门，必须具备以下条件：
- 有权访问表单识别器受限访问预览版。 若要访问预览版，请填写并提交[表单识别器访问请求](https://aka.ms/FormRecognizerRequestAccess)表单。
- 已安装 [cURL](https://curl.haxx.se/windows/)。
- 至少有五个相同类型的表单。 你将使用这些数据来训练模型。 在本快速入门中可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)。 将训练文件上传到 Azure 存储帐户中 blob 存储容器的根目录。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>训练表单识别器模型

首先，你需要 Azure 存储 blob 中的一组训练数据。 应至少有五个类型/结构与主要输入数据相同的已填写表单（PDF 文档和/或图像）。 或者，可以使用一个空表单和两个已填充表单。 空表单的文件名需要包括单词“empty”。 有关整理训练数据的提示和选项，请参阅[为自定义模型生成训练数据集](../build-training-data-set.md)。

若要使用 Azure Blob 容器中的文档训练表单识别器模型，请通过运行下面的 cURL 命令来调用“训练”API。  运行该命令之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为从表单识别器订阅中获取的终结点。
1. 将 `<subscription key>` 替换为从上一步复制的订阅密钥。
1. 将 `<SAS URL>` 替换为 Azure Blob 存储容器的共享访问签名 (SAS) URL。 若要检索 SAS URL，请打开 Microsoft Azure 存储资源管理器，右键单击容器，然后选择“获取共享访问签名”  。 确保选中“读取”  和“列表”  权限，然后单击“创建”  。 然后复制 **URL** 部分中的值。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

你将收到包含以下 JSON 输出的 `200 (Success)` 响应：

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

请记下 `"modelId"` 值。 在后续步骤中需要用到它。
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>从表单中提取键值对和表

接下来，分析某个文档并从中提取键值对和表。 通过运行以下 cURL 命令调用“模型 - 分析”API。  运行该命令之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为从表单识别器订阅中获取的终结点。
1. 将 `<modelID>` 替换为在上一部分收到的模型 ID。
1. 将 `<path to your form>` 替换为表单的文件路径（例如，C:\temp\file.pdf）。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Test** 文件夹下的文件。
1. 将 `<file type>` 替换为文件类型。 支持的类型：`application/pdf`、`image/jpeg`、`image/png`。
1. 将 `<subscription key>` 替换为订阅密钥。


```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>检查响应

系统将返回 JSON 格式的成功响应， 并显示从表单中提取的键值对和表：

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们已使用表单识别器 REST API 和 cURL 训练了一个模型，并在示例案例中运行了该模型。 接下来，请参阅参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)