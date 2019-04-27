---
title: 如何使用批量听录 - 语音服务
titlesuffix: Azure Cognitive Services
description: 如果要听录存储（如 Azure Blob）中的大量音频，则批量听录是理想的选择。 使用专用 REST API 可以通过共享访问签名 (SAS) URI 指向音频文件并异步接收听录。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b389d86fe4d23e3f4ee1c66e4270a74351098129
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059599"
---
# <a name="why-use-batch-transcription"></a>为何使用 Batch 听录？

如果要听录存储（如 Azure Blob）中的大量音频，则批量听录是理想的选择。 使用专用 REST API 可以通过共享访问签名 (SAS) URI 指向音频文件并异步接收听录。

## <a name="prerequisites"></a>必备组件

### <a name="subscription-key"></a>订阅密钥

与语音服务的其他所有功能一样，需要按照[入门指南](get-started.md)通过 [Azure 门户](https://portal.azure.com)创建订阅密钥。 如果计划从基线模型获取听录，则需要创建一个密钥。

>[!NOTE]
> 若要使用批量听录，需要具备语音服务的标准订阅 (S0)。 免费订阅密钥 (F0) 不可用。 有关详细信息，请参阅[定价和限制](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自定义模式

如果计划自定义声学或语言模型，请遵循[自定义声学模型](how-to-customize-acoustic-models.md)和[自定义语言模型](how-to-customize-language-model.md)中的步骤。 若要在批量听录中使用所创模型，则需要其模型 ID。 此 ID 不是在“终结点详细信息”视图中查找到的终结点 ID，而是选择模型“详细信息”时可检索到的模型 ID。

## <a name="the-batch-transcription-api"></a>Batch 听录 API

Batch 听录 API 提供异步语音转文本听录和其他功能。 它是一个 REST API，可以公开用于以下目的的方法：

1. 创建批处理请求
1. 查询状态
1. 下载听录

> [!NOTE]
> Batch 听录 API 非常适合经常累积数千小时音频的呼叫中心。 它使转录大量录音变得简单。

### <a name="supported-formats"></a>支持的格式

Batch 听录 API 支持以下格式：

| 格式 | 编解码器 | Bitrate | 采样率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位 | 8 或 16 kHz、单声道、立体声 |
| MP3 | PCM | 16 位 | 8 或 16 kHz、单声道、立体声 |
| OGG | OPUS | 16 位 | 8 或 16 kHz、单声道、立体声 |

对于立体声音频流，Batch 听录 API 将在听录期间分离左右声道。 根据单个通道创建两个带有结果的 JSON 文件。 开发人员可利用每个话语的时间戳创建有序的最终脚本。 此示例请求包括用于不雅内容筛选、标点符号和字级时间戳的属性。 

### <a name="configuration"></a>配置

配置参数以 JSON 形式提供：

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": ["<optional acoustic model ID>, <optional language model ID>"],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  }
}
```

> [!NOTE]
> Batch 听录 API 使用 REST 服务来请求听录内容、听录状态和相关结果。 可在任何语言中使用该 API。 下一部分将介绍该 API 的用法。

### <a name="configuration-properties"></a>配置属性

| 参数 | 描述 | 必需/可选 |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | 指定如何处理识别结果中的不雅内容。 接受的值为 `none`（禁用不雅内容筛选）、`masked`（将不雅内容替换为星号）、`removed`（从结果中删除所有不雅内容）或 `tags`（添加“不雅内容”标记）。 默认设置是 `masked`。 | 可选 |
| `PunctuationMode` | 指定如何处理识别结果中的标点。 接受的值为 `none`（禁用标点）、`dictated`（表示使用显式标点）、`automatic`（允许解码器处理标点）或 `dictatedandautomatic`（表示使用专用标点符号或自动使用标点）。 | 可选 |
 | `AddWordLevelTimestamps` | 指定是否应将字级时间戳添加到输出。 接受的值为 `true`，其支持字级时间戳和 `false`（默认值）禁用它。 | 可选 |

### <a name="storage"></a>存储

批处理脚本支持[Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)音频和编写转录到存储中读取。

## <a name="webhooks"></a>Webhook 

轮询听录状态不能性能最好，或提供最佳用户体验。 若要轮询状态，可以注册将在长时间运行的脚本任务已完成时通知客户端的回调。

有关更多详细信息，请参阅[Webhook](webhooks.md)。

## <a name="sample-code"></a>代码示例

完整示例可在 `samples/batch` 子目录的 [GitHub 示例存储库](https://aka.ms/csspeech/samples)中获得。

如要使用自定义声学或语言模型，必须使用订阅信息、服务区域、指向要转录的音频文件的 SAS URI 和模型 ID 来自定义示例代码。 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

示例代码将设置客户端并提交转录请求。 然后它将轮询状态信息并打印关于转录进度的详细信息。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

有关上述调用的完整详细信息，请参阅 [Swagger 文档](https://westus.cris.ai/swagger/ui/index)。 有关此处所示的完整示例，请转到 `samples/batch` 子目录中的 [GitHub](https://aka.ms/csspeech/samples)。

请注意用于发布音频和接收听录状态的异步设置。 创建的客户端是一个 .NET HTTP 客户端。 `PostTranscriptions` 方法用于发送音频文件详细信息，`GetTranscriptions` 方法用于接收结果。 `PostTranscriptions` 返回句柄，`GetTranscriptions` 使用此句柄创建一个句柄来获取听录状态。

当前示例代码未指定任何自定义模型。 该服务使用基线模型来听录一个或多个文件。 若要指定模型，可为声学和语言模型传递与模型 ID 相同的方法。

> [!NOTE]
> 对于基线听录，无需声明基线模型的 ID。 如果只指定语言模型 ID（而不指定声学模型 ID），则自动选择匹配的声学模型。 如果只指定声学模型 ID，则自动选择匹配的语言模型。

## <a name="download-the-sample"></a>下载示例

可以在 [GitHub 示例存储库](https://aka.ms/csspeech/samples)的 `samples/batch` 目录中查找到该示例。

> [!NOTE]
> 批量听录作业是在最大努力的基础上进行安排的，没有对作业何时会变为运行状态作时间估计。 进入运行状态后，实际转录处理速度比实时音频快。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
