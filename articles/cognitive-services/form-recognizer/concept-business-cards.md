---
title: 名片-窗体识别器
titleSuffix: Azure Cognitive Services
description: 使用格式识别器 API （使用和限制）了解与名片分析相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 1163531fb5a6aa7158bd81ff9095ed1ee29e73c1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004895"
---
# <a name="business-card-concepts"></a>名片概念

Azure 窗体识别器可以使用其预生成的模型之一来分析和提取名片中的联系人信息。 此名片 API 结合了强大的光学字符识别功能 (OCR) 功能与我们的业务卡理解模型结合了来自名片的信息（英语）。 它提取个人联系信息、公司名称、职务等。 预构建的名片 API 在表单识别器2.1 版预览版中公开提供。 

## <a name="what-does-the-business-card-api-do"></a>名片 API 有什么作用？

名片 API 从名片提取关键字段，并将它们以组织的 JSON 响应返回。

![Contoso 详细的 FOTT + JSON 输出](./media/business-card-english.jpg)

### <a name="fields-extracted"></a>提取的字段：

* 联系人姓名 
  * 名字
  * 姓氏
* 公司名称 
* Departments 
* 职务 
* 电子邮件 
* 网站 
* 地址 
* 电话号码 
  * 移动电话 
  * 传真 
  * 工作电话 
  * 其他电话 

名片 API 还可以从名片返回所有已识别的文本。 此 OCR 输出包含在 JSON 响应中。  

### <a name="input-requirements"></a>输入要求 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>"分析业务智能卡" 操作

" [分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) " 名片会将名片的图像或 PDF 作为输入，并提取相关值。 调用返回一个名为的响应标头字段 `Operation-Location` 。 `Operation-Location`该值是一个 URL，其中包含要在下一步中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>"获取分析业务智能卡" 结果操作

第二步是调用 " [获取分析业务智能卡" 结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeBusinessCardResult) 操作。 此操作采用 "分析业务智能卡" 操作创建的结果 ID 作为输入。 它将返回一个 JSON 响应，该响应包含具有以下可能值的 **状态** 字段。 此操作以迭代方式调用，直到它返回 **成功** 值。 使用3到5秒的间隔，以避免超出每秒 (RPS) 速率的请求数。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|状态 | 字符串 | notStarted：分析操作尚未开始。<br /><br />正在运行：分析操作正在进行。<br /><br />失败：分析操作失败。<br /><br />succeeded：分析操作成功。|

当 " **状态** " 字段的值为 " **成功** " 时，如果请求，JSON 响应将包括业务卡理解和可选的文本识别结果。 业务卡理解结果被组织为命名字段值的字典，其中每个值都包含提取的文本、规范化值、边界框、置信度和对应的单词元素。 文本识别结果组织为带有文本、边界框和置信度信息的线条和单词的层次结构。

![示例名片输出](./media/business-card-results.png)

### <a name="sample-json-output"></a>示例 JSON 输出

请参阅以下成功的 JSON 响应示例： "readResults" 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。 "DocumentResults" 节点包含模型发现的特定于业务的值。 在这里，你将找到有用的联系人信息，如名字、姓氏、公司名称等。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                "lines": 
                          {
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
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
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
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
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
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
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
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

按照 [提取名片数据](./QuickStarts/python-business-cards.md) 快速入门中的步骤，使用 Python 和 REST API 来实现名片数据提取。

## <a name="customer-scenarios"></a>客户方案  

使用名片 API 提取的数据可用于执行各种任务。 提取此联系信息会自动为面向客户端角色的客户节省时间。 以下是我们的客户使用名片 API 完成的几个示例：

* 从名片提取联系人信息并快速创建电话联系人。 
* 与 CRM 集成，使用名片图像自动创建联系人。 
* 跟踪销售线索。  
* 从现有的名片图像批量提取联系信息。 

名片 API 还支持 [AIBuilder 名片处理功能](https://docs.microsoft.com/ai-builder/prebuilt-business-card)。

## <a name="next-steps"></a>后续步骤

- 按照快速入门 [快速入门](./quickstarts/python-business-cards.md)
- 了解 [窗体识别器 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
- 了解有关[窗体识别器](overview.md)的详细信息

