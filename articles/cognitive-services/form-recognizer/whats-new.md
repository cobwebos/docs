---
title: 表单识别器中的新增功能
titleSuffix: Azure Cognitive Services
description: 了解窗体识别器 API 的最新更改。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: cb5639dcf0e13ea03d34604816b3939085674c2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456362"
---
# <a name="whats-new-in-form-recognizer"></a>表单识别器中的新增功能

本文重点介绍了新版本的窗体识别器 API 所提供的重大更改。

> [!NOTE]
> 除非另行指定，否则此文档集中的快速入门和指南始终使用最新版本的 API。

## <a name="form-recognizer-20-preview"></a>表单识别器2.0 （预览版）

> [!IMPORTANT]
> 窗体识别器2.0 当前可用于 `West US 2` 和 `West Europe` 区域中的订阅。 如果你的订阅不在此区域中，请使用 1.0 API。 用于培训和使用自定义模型的快速入门可用于 v1.0 和 v2.0。

### <a name="new-features"></a>新增功能

* **自定义模型**
  * **标签定型**你现在可以使用手动标记的数据来训练自定义模型。 这会生成更好的模型，并且可以生成处理复杂的窗体或窗体（其中包含没有键的值）的模型。
  * **异步 API**可以使用异步 API 调用来训练和分析大型数据集和文件。
  * **TIFF 文件支持**你现在可以训练和提取 TIFF 文档中的数据。
  * **提取准确性改进**

* **预生成的接收模型**
  * **Tip 数量**你现在可以提取 tip 金额和其他手写值。
  * **行项提取**您可以从收据中提取行项值。
  * **置信度值**您可以查看模型对每个提取值的置信度。
  * **提取准确性改进**

* **布局提取**现在可以使用布局 API 从窗体中提取文本数据和表数据。

### <a name="custom-model-api-changes"></a>自定义模型 API 更改

所有用于定型和使用自定义模型的 Api 都已重命名，并且一些同步方法现在是异步的。 下面是重大更改：

* 模型定型过程现在是异步的。 通过 **/Custom/models** API 调用启动培训。 此调用将返回一个操作 ID，你可以将该 ID 传递到**自定义/模型/{modelID}** 以返回定型结果。
* 键/值提取现在由 **/Custom/models/{modelID}/analyze** API 调用启动。 此调用将返回一个操作 ID，你可以将该 ID 传递到**自定义/模型/{modelID}/analyzeResults/{resultID}** 以返回提取结果。
* 训练操作的操作 Id 现在位于 HTTP 响应的**Location**标头中，而不是位于**操作位置**标头。

### <a name="receipt-api-changes"></a>接收 API 更改

用于读取销售收据的 Api 已重命名。

* 接收数据提取现在由 **/Prebuilt/receipt/analyze** API 调用启动。 此调用将返回一个操作 ID，你可以将该 ID 传递到 **/prebuilt/receipt/analyzeResults/{resultID}** 以返回提取结果。

### <a name="output-format-changes"></a>输出格式更改

所有 API 调用的 JSON 响应都具有新格式。 添加、删除或重命名了某些键和值。 请参阅快速入门，了解当前 JSON 格式的示例。

## <a name="next-steps"></a>后续步骤

完成一个[快速入门](quickstarts/curl-train-extract.md)，开始使用[表单识别器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)。