---
title: 升级到计算机视觉 API 的 v3.0
titleSuffix: Azure Cognitive Services
description: 了解如何从计算机视觉 API 的 v2.0 和 v2.1 升级到 v3.0。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 16add0dce88d0f809dc291d3c9de33e1a853f257
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136566"
---
# <a name="upgrade-to-v30-of-computer-vision-api-from-v20-and-v21"></a>从计算机视觉 API 的 v2.0 和 v2.1 升级到 v3.0

本指南演示如何修改现有代码，以便为 REST API 的用户从计算机视觉 API 的 v2.0 或 v2.1 迁移到 v3.0。 

## <a name="upgrade-batch-read-file-to-read"></a>将 `Batch Read File` 升级到 `Read`


1. 按如下所示更改 `Batch Read File` 2.x 的 API 路径：


    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/v2.0/read/core/asyncBatchAnalyze     |https://{endpoint}/vision/v3.0/read/analyze[?language]|
    
    新的可选 language 参数可用。 如果你不知道文档的语言，或者文档可能采用多语言，请不要包括该参数。 

2. 按如下所示更改 2.x 中 `Get Read Results` 的 API 路径：

    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/v2.0/read/operations/{operationId}      |https://{endpoint}/vision/v3.0/read/analyzeResults/{operationId} |

3. 更改用于检查 `Get Read Operation Result` 的 json 结果的代码。 对 `Get Read Operation Result` 的调用成功时，它将在 JSON 正文中返回状态字符串字段。 v2.0 中的以下值已更改，以便更好地与其他认知服务 REST API 保持一致。 
 
    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. 更改代码以解释 `Get Read Operation Result` 中的最终识别结果 JSON。 

    请注意以下对 json 的更改：
    
    - 在 v2.x 中，若状态为 `"Succeeded"`，`"Get Read Operation Result"` 将返回 OCR 识别 json。 在 v3.0 中，该字段为 `"succeeded"`。
    - 要获得页面数组的根，请将 json 层次结构从 `"recognitionResults"` 更改为 `"analyzeResult"`/`"readResults"`。 每页的行和字 json 层次结构保持不变，因此不需要更改代码。
    -   页面角度 `"clockwiseOrientation"` 已重命名为 `"angle"`，且范围已从 0 - 360 度更改为 -180 - 180 度。 根据你的代码，你可能需要或不必进行更改，因为大多数数学函数都可以处理两者中的任一范围。
    -   此外，v3.0 API 还引入了以下你可以选择性地利用的改进：-`"createdDateTime"` 和 `"lastUpdatedDateTime"` 已添加，使你可以跟踪处理的持续时间。 有关更多详细信息，请参阅文档。 
        - `"version"` 指示用于生成结果的 API 版本
        - 已添加每个字的 `"confidence"`。 该值经过校准，使得值 0.95 表示识别正确的概率为 95%。 置信度分数可用于选择要发送到人工审阅的文本。 
    
    
        在 2.X 中，输出格式如下所示： 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    在 v3.0 中，已对其进行了调整：
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```

## <a name="upgrade-from-recognize-text-to-read"></a>从 `Recognize Text` 升级到 `Read`
`Recognize Text` 是预览操作，在计算机视觉 API 的所有版本中将被弃用 。 必须从 `Recognize Text` 迁移到 `Read` (v3.0) 或 `Batch Read File`（v2.0、v2.1）。 `Read` v3.0 包括更新、更好的文本识别和附加功能模型，因此建议使用它。 从 `Recognize Text` 升级到 `Read`：

1. 按如下所示更改 `Recognize Text` v2.x 的 API 路径：


    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/v2.0/recognizeText[?mode]|https://{endpoint}/vision/v3.0/read/analyze[?language]|
    
    `Read` 中不支持 mode 参数。 手写文本和打印文本都将自动受支持。
    
    新的可选 language 参数在 v3.0 中可用。 如果你不知道文档的语言，或者文档可能采用多语言，请不要包括该参数。 

2. 按如下所示更改 `Get Recognize Text Operation Result` v2.x 的 API 路径：

    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/v2.0/textOperations/{operationId}|https://{endpoint}/vision/v3.0/read/analyzeResults/{operationId}|

3. 更改用于检查 `Get Recognize Text Operation Result` 的 json 结果的代码。 对 `Get Read Operation Result` 的调用成功时，它将在 JSON 正文中返回状态字符串字段。 
 
    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. 更改代码，解释 `Get Recognize Text Operation Result` 中的最终识别结果 JSON 以支持 `Get Read Operation Result`。

    请注意以下对 json 的更改：    

    - 在 v2.x 中，若状态为 `"Succeeded"`，`"Get Read Operation Result"` 将返回 OCR 识别 json。 在 v3.0 中，该字段为 `"succeeded"`。
    - 要获得页面数组的根，请将 json 层次结构从 `"recognitionResult"` 更改为 `"analyzeResult"`/`"readResults"`。 每页的行和字 json 层次结构保持不变，因此不需要更改代码。
    -   v3.0 API 还引入了以下改进，你可以选择性地利用这些改进。 有关详细信息，请参阅 API 参考：-`"createdDateTime"` 和 `"lastUpdatedDateTime"` 已添加，使你可以跟踪处理的持续时间。 有关更多详细信息，请参阅文档。 
        - `"version"` 指示用于生成结果的 API 版本
        - 已添加每个字的 `"confidence"`。 该值经过校准，使得值 0.95 表示识别正确的概率为 95%。 置信度分数可用于选择要发送到人工审阅的文本。 
        - `"angle"`：文本在顺时针方向上的大致方向，以 (-180, 180] 之间的度数度量。
        -  `"width"` 和 `"height"`：提供了文档的尺寸；`"unit"`：提供了这些尺寸的单位（像素或英尺，具体取决于文档类型。）
        - `"page"`：多页文档受支持
        - `"language"`：文档的输入语言（来自可选的 language 参数。）


    在 2.X 中，输出格式如下所示： 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    在 v3.0 中，已对其进行了调整：
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```
    
## <a name="all-other-operations"></a>所有其他操作

计算机视觉 API 的 v2.X 和 v3.0 之间没有其他中断性变更。 可以直接修改 API 路径，将 `v2.0` 替换为 `v3.0`。