---
title: Swagger 文档-语音服务
titleSuffix: Azure Cognitive Services
description: Swagger 文档可用于自动生成适用于多种编程语言的 SDK。 Swagger 支持服务中的所有操作
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: dd9ac9654f916653af974e816485630423466ae5
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815429"
---
# <a name="swagger-documentation"></a>Swagger 文档

语音服务提供 Swagger 规范，以与用于导入数据、创建模型、测试模型准确性、创建自定义终结点、排队批处理转录和管理订阅的少量 REST Api 进行交互。 可使用这些 API 以编程方式完成通过自定义语音门户提供的大部分操作。

> [!NOTE]
> 支持将语音转文本和文本转语音操作作为 REST API，而后者记录在 Swagger 规范中。

## <a name="generating-code-from-the-swagger-specification"></a>从 Swagger 规范生成代码

[Swagger 规范](https://cris.ai/swagger/ui/index)包含可快速测试各种路径的选项。 但有时需要为所有路径生成代码，从而创建可基于未来的解决方案的单个调用库。 让我们看看生成 Python 库的过程。

需要将 Swagger 设置为与语音服务订阅相同的区域。 可以在语音服务资源下的 Azure 门户中确认区域。 有关受支持的区域的完整列表，请参阅[区域](regions.md)。

1. 转到 https://editor.swagger.io
2. 单击“文件”，然后单击“导入”
3. 输入 swagger URL，其中包括你的语音服务订阅的区域 `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. 单击“生成客户端”，然后选择 Python
5. 保存客户端库

你可以使用使用[GitHub 上的语音服务示例](https://aka.ms/csspeech/samples)生成的 Python 库。

## <a name="reference-docs"></a>参考文档

* [REST （Swagger）：批处理脚本和自定义](https://westus.cris.ai/swagger/ui/index)
* [REST API：语音到文本](rest-speech-to-text.md)
* [REST API：文本到语音转换](rest-text-to-speech.md)

## <a name="next-steps"></a>后续步骤

* [GitHub 上的语音服务示例](https://aka.ms/csspeech/samples)。
* [免费获取语音服务订阅密钥](get-started.md)
