---
title: 批次测试 TSV 格式 - QnA 制造商
titleSuffix: Azure Cognitive Services
description: 了解批次测试的 TSV 格式
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507821"
---
# <a name="batch-testing-tsv-format"></a>批处理测试 TSV 格式

批处理测试可从[源代码](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)或可[下载的可执行压缩 。](https://aka.ms/qna_btzip) 运行批处理测试的命令的格式为：

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|预期值|
|--|--|
|1|使用[TSV 输入字段](#tsv-input-fields)格式化的 tsv 文件的名称|
|2|用于终结点的 URI，使用 QnA Maker 门户的发布页面中的"您的-HOST"。|
|3|ENDPOINT-KEY，位于 QnA 制造商门户的发布页面上。|
|4|由批处理测试创建的结果的 tsv 文件的名称。|

使用以下信息了解和实现用于批处理测试的 TSV 格式。 

## <a name="tsv-input-fields"></a>TSV 输入字段

|TSV 输入文件字段|说明|
|--|--|
|KBID|在"发布"页上找到的 KB ID。|
|问题|用户将输入的问题。|
|元数据标记|可选|
|Top 参数|可选| 
|预期的答案 ID|可选|

![用于批处理测试的 TSV 文件的输入格式。](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 输出字段 

|TSV 输出文件参数|说明|
|--|--|
|KBID|在"发布"页上找到的 KB ID。|
|问题|从输入文件输入的问题。|
|Answer|知识库中的首要答案。|
|答案 ID|答案 ID|
|Score|答案的预测分数。 |
|元数据标记|与返回的答案关联|
|预期的答案 ID|可选（仅在给出预期答案 ID 时）|
|判断标签|可选，值可以是：正确或不正确（仅在给出预期答案时）|
