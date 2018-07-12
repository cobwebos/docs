---
title: 调用用于视频的情感 API | Microsoft Docs
description: 了解如何在认知服务中调用用于视频的情感 API。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 0875013b2061a84e3e23ae90c1106382672fdca6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365749"
---
# <a name="how-to-call-emotion-api-for-video"></a>如何调用用于视频的情感 API

> [!IMPORTANT]
> 视频 API 预览将于 2017 年 10 月 30 日结束。 请试用新的[视频索引器 API 预览](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，通过检测口语、人脸、字符和情感，轻松地从视频中提取见解，增强内容发现体验，例如搜索结果。 [了解详细信息](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

本指南演示如何调用用于视频的情感 API。 这些示例是使用用于视频的情感 API 客户端库以 C# 编写的。

### <a name="Prep">准备工作</a> 
若要使用用于视频的情感 API，你需要一个包含人物的视频，最好是人们面向摄像头的视频。

### <a name="Step1">步骤 1：授权 API 调用</a> 
每次调用用于视频的情感 API 都需要提供订阅密钥。 此密钥需要通过查询字符串参数传递，或者在请求头中指定。 若要通过查询字符串传递订阅密钥，请参阅用于视频的情感 API 的以下请求 URL 作为示例：

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

作为替代方法，也可以在 HTTP 请求头中指定订阅密钥：

```
ocp-apim-subscription-key: <Your subscription key>
```

使用客户端库时，订阅密钥通过 VideoServiceClient 类的构造函数传递。 例如：

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
若要获取订阅密钥，请参阅[订阅] (https://azure.microsoft.com/try/cognitive-services/)。 

### <a name="Step2">步骤 2：将视频上传到服务并检查状态</a>
在执行任何用于视频的情感 API 调用时，最基本的方法是直接上传视频。 此操作通过发送内容类型为 application/octet-stream 且包含从视频文件中读取的数据的“POST”请求完成。 视频的最大大小为 100MB。

使用客户端库时，借助上传实现的稳定是通过传入流对象完成的。 请参阅以下示例：

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

请注意，VideoServiceClient 的 CreateOperationAsync 方法是异步的。 调用方法也应标记为异步，以便使用 await 子句。
如果视频已发布到 Web 上，并且具有公共 URL，则可以通过提供该 URL 来访问用于视频的情感 API。 在本示例中，请求正文将为 JSON 字符串，其中包含该 URL。

使用客户端库时，借助 URL 实现的稳定可通过 CreateOperationAsync 方法的另一个重载轻松执行。


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

对于所有用于视频的情感 API 调用，此上传方法都一样。 

上传视频后，你要执行的下一个操作是检查其状态。 由于视频文件通常比其他文件更大且更多样化，因此用户在这一步可能需要很长的处理时间。 具体时间取决于文件的大小和长度。

使用客户端库时，可以使用 GetOperationResultAsync 方法检索操作状态和结果。


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
通常，客户端应定期检索操作状态，直到状态显示为“成功”或“失败”。

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

当 VideoOperationResult 的状态显示为“成功”时，可以通过将 VideoOperationResult 强制转换为 VideoOperationInfoResult<VideoAggregateRecognitionResult> 并访问 ProcessingResult 字段来检索结果。

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">步骤 3：检索和了解情感识别并跟踪 JSON 输出</a>

输出结果以 JSON 格式包含给定文件中人脸的元数据。

如步骤 2 中所述，当 OperationResult 的状态显示为“成功”时，其 ProcessingResult 字段中会显示 JSON 输出。

人脸检测和跟踪 JSON 包括以下属性：

属性 | 说明
-------------|-------------
版本 | 指用于视频的情感 API 的 JSON 版本。
时间刻度 | 视频的每秒“刻度”数。
Offset  |时间戳的时间偏移量。 在用于视频的情感 API 1.0 版中，它始终为 0。 在将来支持的方案中，此值可能会更改。
Framerate | 视频的每秒帧数。
Fragments   | 元数据被切割成称为片段的不同小块。 每个片段包含开始时间、持续时间、间隔数字和事件。
开始   | 第一个事件的开始时间（以刻度为单位）。
Duration |  片段的长度（以刻度为单位）。
时间间隔 |  片段中每个事件的长度（以刻度为单位）。
活动  | 一个事件数组。 外部数组代表一个时间间隔。 内部数组包含在该时间点发生的 0 个或多个事件。
windowFaceDistribution |    在事件期间拥有特定情感的人脸百分比。
windowMeanScores |  图像中人脸的每种情感的平均分数。

以这种方式格式化 JSON 是为了针对将来的方案设置 API，在这些方案中，快速检索元数据并管理大量结果非常重要。 这种格式化同时采用了碎片技术（允许分解基于时间的部分中的元数据，你可以只下载所需的内容）和分段技术（允许在事件过大时分解事件）。 一些简单的计算可帮助你转换数据。 例如，如果事件从 6300（刻度）开始，其时间刻度为 2997（刻度/秒），帧速率为 29.97（帧/秒），那么：

*   开始时间/时间刻度 = 2.1 秒
*   秒数 x (帧速率/时间刻度) = 63 帧

下面是针对面部检测和跟踪，将 JSON 提取为每帧格式的简单示例：

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
由于情感会随时间推移而消失，因此，如果生成一个可视化效果以将结果覆盖在原始视频之上，则将提供的时间戳减去 250 毫秒。

### <a name="Summary">摘要</a>
在本指南中，你已了解用于视频的情感 API 的各项功能：如何上传视频，检查其状态，检索情感识别元数据。

有关 API 详情的详细信息，请参阅 API 参考指南“[用于视频的情感 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)”。
