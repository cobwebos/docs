---
title: 快速入门：使用 cURL 训练模型并提取表单数据 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用表单识别器 REST API 和 cURL 来训练模型，并从表单中提取数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/03/2019
ms.author: pafarley
ms.openlocfilehash: 16837ff53d7a87f6d6ac86643c7c8d16721e9470
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660369"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>快速入门：使用 REST API 和 cURL 训练表单识别器模型并提取表单数据

在本快速入门中，我们将使用 Azure 表单识别器的 REST API 和 cURL 来训练表单并为其评分，以提取键值对和表。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 本快速入门使用表单识别器 v2.0 API。 如果你的订阅不在 `West US 2` 或 `West Europe` 区域中，则需要使用 v1.0 API。 改为按照 [v1.0 快速入门](./curl-train-extract-v1.md)。

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，必须具备以下条件：
- 有权访问表单识别器受限访问预览版。 若要访问预览版，请填写并提交[表单识别器访问请求](https://aka.ms/FormRecognizerRequestAccess)表单。
- 已安装 [cURL](https://curl.haxx.se/windows/)。
- 至少有五个相同类型的表单。 你将使用这些数据来训练模型。 表单可以是不同的文件类型，但必须是相同的文档类型。 在本快速入门中可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)。 将训练文件上传到 Azure 存储帐户中 blob 存储容器的根目录。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>训练表单识别器模型

首先，你需要 Azure 存储 blob 中的一组训练数据。 应至少有五个类型/结构与主要输入数据相同的已填写表单（PDF 文档和/或图像）。 或者，可以使用一个空表单和两个已填充表单。 空表单的文件名需要包括单词“empty”。 有关整理训练数据的提示和选项，请参阅[为自定义模型生成训练数据集](../build-training-data-set.md)。

> [!NOTE]
> 可以使用标记数据功能来手动预先标记部分或全部训练数据。 这是一个更为复杂的过程，但会生成更好的经过训练的模型。 有关此功能的详细信息，请参阅概述的[通过标签进行训练](../overview.md#train-with-labels)部分。

若要使用 Azure Blob 容器中的文档训练表单识别器模型，请运行下面的 cURL 命令来调用[训练自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) API  。 运行该命令之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为从表单识别器订阅中获取的终结点。
1. 将 `<subscription key>` 替换为从上一步复制的订阅密钥。
1. 将 `<SAS URL>` 替换为 Azure Blob 存储容器的共享访问签名 (SAS) URL。 若要检索 SAS URL，请打开 Microsoft Azure 存储资源管理器，右键单击容器，然后选择“获取共享访问签名”  。 确保选中“读取”  和“列表”  权限，然后单击“创建”  。 然后复制 **URL** 部分中的值。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

你将收到包含“Location”标头的 `201 (Success)` 响应  。 此标头的值是要训练的新模型的 ID。 

## <a name="get-training-results"></a>获取训练结果

开始训练操作后，可以使用新操作[获取自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)检查训练状态  。 将模型 ID 传递到此 API 调用以检查训练状态：

1. 将 `<Endpoint>` 替换为从表单识别器订阅密钥中获得的终结点。
1. 将 `<subscription key>` 替换为订阅密钥
1. 将 `<model ID>` 替换为在上一步骤收到的模型 ID

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

你将收到以下格式的 JSON 正文的 `200 (Success)` 响应。 请注意 `"status"` 字段。 完成训练后，将出现值 `"ready"`。 如果模型没有完成训练，则需要重新运行该命令以再次查询该服务。 我们建议两次调用间隔一秒或更长时间。

`"modelId"` 字段包含要训练的模型的 ID。 下一步骤需要用到此字段。

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>分析键值对和表的表单

接下来，使用新的经过训练的模型分析文档并从中提取键值对和表。 运行以下 cURL 命令来调用[分析表单](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) API  。 运行该命令之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为从表单识别器订阅密钥中获取的终结点。 可以在表单识别器资源的“概览”选项卡中找到该终结点。 
1. 将 `<model ID>` 替换为在上一部分收到的模型 ID。
1. 将 `<path to your form>` 替换为表单的文件路径（例如，C:\temp\file.pdf）。 也可以是指向远程文件的 URL。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Test** 文件夹下的文件。
1. 将 `<file type>` 替换为文件类型。 支持的类型：`application/pdf`、`image/jpeg`、`image/png`、`image/tiff`。
1. 将 `<subscription key>` 替换为订阅密钥。

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

你将收到 `202 (Success)` 响应，其中包含“Operation-Location”标头  。 此标头的值是用于跟踪“分析”操作结果的 ID。 保存此 ID 以供下一步使用。

## <a name="get-the-analyze-results"></a>获取分析结果

使用以下 API 查询“分析”操作的结果。

1. 将 `<Endpoint>` 替换为从表单识别器订阅密钥中获取的终结点。 可以在表单识别器资源的“概览”选项卡中找到该终结点。 
1. 将 `<result ID>` 替换为上一部分中收到的 ID。
1. 将 `<subscription key>` 替换为订阅密钥。

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

你将收到以下格式的 JSON 正文的 `200 (Success)` 响应。 为了简单起见，已缩短输出。 请注意底部附近的 `"status"` 字段。 完成“分析”操作时，会出现 `"succeeded"` 值。 如果“分析”操作尚未完成，则需要重新运行该命令以再次查询该服务。 我们建议两次调用间隔一秒或更长时间。

主键/值对关联和表位于 `"pageResults"` 节点中。 如果还通过 includeTextDetails URL 参数指定了纯文本提取，则 `"readResults"` 节点将显示文档中所有文本的内容和位置  。

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>改进结果

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们已使用表单识别器 REST API 和 cURL 训练了一个模型，并在示例案例中运行了该模型。 接下来，请参阅参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
