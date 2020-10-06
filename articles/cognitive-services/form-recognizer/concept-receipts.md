---
title: 回执-窗体识别器
titleSuffix: Azure Cognitive Services
description: 了解与采用格式识别器 API （使用和限制）相关的接收分析相关概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 0382c7c7f7d068ea227397ae7accf4bc410de04a
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761441"
---
# <a name="receipt-concepts"></a>回执概念

Azure 窗体识别器可以使用它的一个预生成模型分析回执。 接收方 API 从销售收据提取关键信息，如商家名称、交易日期、交易总计、行项等。 

## <a name="understanding-receipts"></a>了解回执 

许多企业和个人仍然依赖于从其销售收据手动提取数据，无论是出于业务开支报告、偿还、审核、税务目的、预算、市场营销或其他目的。 通常，在这种情况下，需要提供物理收据的图像来进行验证。  

自动从这些收据提取数据可能会很复杂。 收据可能 crumpled、打印或手写部分，收据的智能手机图像可能质量较低。 另外，收据模板和字段在市场、区域和商家上可能会有很大的差异。 数据提取和现场检测中的这些挑战都使接收处理成为一个独特的问题。  

使用光学字符识别 (OCR) 和预生成的接收模型，接收 API 可实现这些接收处理方案，并从收据中提取数据，例如商人名称、提示、总计、行项等等。 使用此 API 时，无需对模型进行定型，只需将收据发送到分析回执 API 并提取数据。

![收据示例](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>回执 API 有什么作用？ 

预生成的回执 API 提取销售回执的内容， &mdash; 即您通常会获得餐馆、零售商或杂货店的收据类型。

### <a name="fields-extracted"></a>提取的字段

* 商家名称 
* 商家地址 
* 商家电话号码 
* 事务日期 
* Transaction Time（事务时间） 
* 小计 
* 税款 
* 总计 
* 提示 
* 行项提取 (例如，项数量、项价格、项名称) 

### <a name="additional-features"></a>其他功能

接收 API 还会返回以下信息：

* 收据类型 (如明细化、信用卡等) 
* 字段置信度 (每个字段都返回关联的置信度值) 
* OCR raw 文本 (用于整接收的 OCR 提取文本输出) 
* 每个值、行和字的边界框

## <a name="input-requirements"></a>输入要求

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>支持的区域设置 

* **预先构建的接收** V2.0 (GA) 支持 en-us 区域设置中的销售收据
* **预先生成的收条2.1 版-预览版** (公开预览版) 增加了对以下 EN 收据区域设置的其他支持： 
  * EN AU 
  * EN-CA 
  * EN-GB 
  * EN 

  > [!NOTE]
  > 语言输入 
  >
  > 预生成的收条2.1 版-预览版。1包含一个可选的 request 参数，用于指定来自其他英语市场的回执区域设置。 对于澳大利亚英语 (EN-US) 、加拿大 (EN-CA) 、英国 () 和印度 (EN-US) 中的销售收据，可以指定区域设置以获得改进的结果。 如果未在 v 2.1-preview. 1 中指定区域设置，则模型将默认为 EN-US 模型。


## <a name="the-analyze-receipt-operation"></a>分析回执操作

[分析收据](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeReceiptAsync)采用收据的图像或 PDF 作为输入，并提取相关值和文本。 调用返回一个名为的响应标头字段 `Operation-Location` 。 `Operation-Location`该值是一个 URL，其中包含要在下一步中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>获取分析回执结果操作

第二步是调用 [Get 分析回执结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeReceiptResult) 操作。 此操作采用由分析回执操作创建的结果 ID 作为输入。 它将返回一个 JSON 响应，该响应包含具有以下可能值的 **状态** 字段。 此操作以迭代方式调用，直到它返回 **成功** 值。 使用3到5秒的间隔，以避免超出每秒 (RPS) 速率的请求数。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | 字符串 | notStarted：分析操作尚未开始。 |
| |  | 正在运行：分析操作正在进行。 |
| |  | 失败：分析操作失败。 |
| |  | succeeded：分析操作成功。 |

当 " **状态** " 字段的值为 " **成功** " 时，JSON 响应将包括 "接收了解" 和 "文本识别" 结果。 接收方理解结果组织为命名字段值的字典，其中每个值都包含提取的文本、规范化值、边界框、置信度和对应的单词元素。 文本识别结果组织为带有文本、边界框和置信度信息的线条和单词的层次结构。

![示例接收结果](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>示例 JSON 输出

请参阅以下成功的 JSON 响应示例： "readResults" 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。 "DocumentResults" 节点包含模型发现的特定于业务的值。 在这里，你将找到有用的键/值对，如名字、姓氏、公司名称等。

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```


## <a name="customer-scenarios"></a>客户方案  

使用收条 API 提取的数据可用于执行各种任务。 下面是我们的客户使用回执 API 完成的几个示例。 

### <a name="business-expense-reporting"></a>业务支出报表  

通常，归档业务开支需要花费时间从收据的图像中手动输入数据。 使用接收 API，可以使用提取的字段部分自动执行此过程，并快速分析收据。  

由于接收 API 具有简单的 JSON 输出，因此可以通过多种方式使用提取的字段值。 与内部费用应用程序集成，以预填充费用报告。 有关此方案的详细信息，请阅读 Acumatica 如何利用接收 API 来 [使费用报告的过程更少](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure)。  

### <a name="auditing-and-accounting"></a>审核和记帐 

还可以使用 "回执 API" 输出在开支报告和补偿过程中的不同时间点对大量支出进行分析。 您可以处理回执，以便对其进行手动审核或快速批准。  

收据输出还适用于一般书籍，以便企业或个人使用。 使用收条 API 将任何原始的接收图像/PDF 数据转换为可操作的数字输出。

### <a name="consumer-behavior"></a>使用者行为 

回执包含有用的数据，可用于分析消费者行为和购物趋势。

回执 API 还支持 [AIBuilder 回执处理功能](https://docs.microsoft.com/ai-builder/prebuilt-receipt-processing)。

## <a name="next-steps"></a>后续步骤

- 完成 [表单识别器客户端库快速入门](quickstarts/client-library.md) ，开始使用所选语言的窗体识别器编写收据处理应用。
- 或者，按照 [收条 API Python 快速入门](./quickstarts/python-receipts.md) ，使用 REST API 来识别收入。

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
* [REST API 参考文档](https://docs.microsoft.com/azure/cognitive-services/form-recognizer)
