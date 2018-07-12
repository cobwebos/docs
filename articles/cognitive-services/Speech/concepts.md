---
title: 概念 | Microsoft Docs
description: Microsoft 语音服务中使用的基本概念。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: bc23f4fb7dfc045a0f8cc87155c31875c4de8450
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365703"
---
# <a name="basic-concepts"></a>基本概念

本页介绍 Microsoft 语音识别服务中的一些基本概念。 建议在应用程序中使用 Microsoft 语音识别 API 之前阅读此页面。

## <a name="understanding-speech-recognition"></a>了解语音识别

如果这是你第一次创建支持语音的应用程序，或第一次将语音功能添加到现有应用程序，本节将指导你入门。 如果在支持语音的应用程序方面具备一定经验，则可以选择快速浏览本节，或者，如果你经验丰富并且想要直接了解协议详细信息，则可以完全跳过本节。

### <a name="audio-streams"></a>音频流

语音的基本概念中最重要的是音频流。 与击键不同，击键发生在单个时间点并且包含单条信息，而语音请求历时数百毫秒并且包含若干千字节大小的信息。 口述话语的持续时间给希望为其应用程序提供简练顺畅的语音体验的开发者造成了一定的困难。 如今的计算机和算法执行语音听录的时间约占话语持续时间的一半，也就是在大约 1 秒内可以听录 2 秒的话语，但是任何在处理用户时经历 1 秒延迟的应用程序都无法实现简练顺畅。

好在有一些方法可以“隐藏”听录时间，即在用户说出话语后一部分的同时，对话语的前一部分执行听录。 例如，通过将 1 秒钟的话语分成 10 个 100 毫秒的区块，并依次对每个区块执行听录，可在总共听录 500 毫秒中“隐藏”超过 450 毫秒，使用户在说话时不会知道正在进行听录。 联系此示例时，请记住该服务对音频的前一个 100 毫秒执行听录，而此时用户正在说出下一个 100 毫秒，所以当用户停止讲话时，该服务只需要听录大约 100 毫秒的音频来制作结果。

为了实现这种用户体验，将采用区块形式收集语音音频信息，并在用户说话时进行听录。 这些音频区块全部来自于音频流，将这些音频区块发送到服务的过程称为流式传输音频。 流式传输音频是任何支持语音的应用程序的重要组成部分；调整区块大小和优化流式传输的执行是改善应用程序用户体验的最有效方法。

### <a name="microphones"></a>麦克风

人类通过耳朵处理语音，而无生命的硬件则需借助麦克风。 若要在任何应用程序中启用语音，需要与麦克风集成，为应用程序提供音频流。

麦克风的 API 必须支持开始和停止从麦克风接收音频字节。 你需要确定哪些用户操作会触发麦克风开始侦听语音。 可以选择按下按钮触发开始侦听，或者可以选择让关键字或唤醒字检测器始终侦听麦克风，并使用该模块的输出来触发向 Microsoft 语音服务发送音频。

### <a name="end-of-speech"></a>语音结束

对于人类而言，检测说话者何时停止说话似乎非常简单，但在实验室条件之外，这却成为了一个相当困难的问题。 只识别话语后面的静音是不够的，因为经常会有大量环境噪音干扰识别过程。 Microsoft 语音服务能够出色地快速检测用户停止说话的时间，并且可将这一事实告知应用程序，但这种安排意味着应用程序在流程中最后一个知道用户何时停止说话。 这完全不像其他形式的输入，在其他形式的输入中，应用程序第一个知道用户输入何时开始和结束。

### <a name="asynchronous-service-responses"></a>异步服务响应

应用程序需要知道用户输入的完成时间，这一事实不会对应用程序造成任何性能损失或编程困难，但它要求你用与你熟悉的输入请求/响应模式不同的方式来考虑语音请求。 由于应用程序不知道用户何时停止说话，因此应用程序必须继续将音频流式传输到服务，同时同步和异步等待来自服务的响应。 此模式与其他请求/响应 Web 协议（例如 HTTP）不同。 在这些协议中，必须在收到任何响应之前完成请求；而在 Microsoft 语音服务协议中，当仍在向请求流式传输音频的同时，就能收到响应。

> [!NOTE]
> 使用语音 HTTP REST API 时不支持此功能。

### <a name="turns"></a>轮次

语音是信息的载体。 当你说话时，你试图将你掌握的信息传达给正在倾听该信息的人。 传达信息时，人们通常会交替说话和聆听。 同样，支持语音的应用程序通过交替侦听和响应与用户进行交互，尽管应用程序执行的大部分工作都是侦听。 用户的语音输入和对此输入的服务响应称为轮次。 一个轮次开始于用户说话时，结束于应用程序完成对语音服务响应的处理时。

### <a name="telemetry"></a>遥测

即使对于经验丰富的开发者来说，创建支持语音的设备或应用程序也具有挑战性。 基于流的协议乍一看会让人望而生畏，诸如静音检测等重要细节也可能是全新内容。 由于完成单个请求/响应对需要成功发送和接收如此多的消息，因此收集有关这些消息的完整且准确的数据至关重要。 Microsoft 语音服务协议提供对此数据的收集。 应尽可能准确地提供所需数据；通过提供完整且准确的数据，可实现自助式排查客户端实施问题（如有需要，也可从 Microsoft 语音服务团队获得帮助），收集的遥测数据的质量对于问题分析至关重要。

> [!NOTE]
> 使用语音识别 REST API 时不支持此功能。

### <a name="speech-application-states"></a>语音应用程序状态

在应用程序中启用语音输入所采取的步骤与启用其他形式的输入（例如鼠标单击或手指点击）的步骤略有不同。 必须跟踪应用程序侦听麦克风并将数据发送到语音服务的时间，等待服务响应的时间，以及处于空闲状态的时间。 这些状态之间的关系如下图所示。

![语音应用程序状态图](Images/speech-application-state-diagram.png)

由于 Microsoft 语音服务参与某些状态，因此服务协议定义了有助于应用程序在状态之间转换的消息。 应用程序需要解释并处理这些协议消息，以跟踪和管理语音应用程序状态。

## <a name="using-the-speech-recognition-service-from-your-apps"></a>使用应用中的语音识别服务

Microsoft 语音识别服务为开发者提供了两种向其应用添加语音的方法。

- [REST API](GetStarted/GetStartedREST.md)：开发者可使用从其应用到服务的 HTTP 调用来进行语音识别。
- [客户端库](GetStarted/GetStartedClientLibraries.md)：对于高级功能，开发者可下载 Microsoft 语音客户端库，并链接到其应用。  客户端库支持使用不同语言（C#、Java、JavaScript、ObjectiveC）的多种平台（Windows、Android、iOS）。

| 用例 | [REST API](GetStarted/GetStartedREST.md) | [客户端库](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| 转换短语音音频，例如无中间结果的命令（音频长度 < 15 s） | 是 | 是 |
| 转换长音频 (> 15 s) | 否 | 是 |
| 流式传输含所需中间结果的音频 | 否 | 是 |
| 了解使用 LUIS 从音频转换的文本 | 否 | 是 |

 如果你的语言或平台还没有 SDK，可基于[协议文档](API-Reference-REST/websocketprotocol.md)创建自己的实现。

## <a name="recognition-modes"></a>识别模式

有三种识别模式：`interactive`、`conversation` 和 `dictation`。 识别模式基于用户可能说话的方式来调整语音识别。 为应用程序选择适当的识别模式。

> [!NOTE]
> 识别模式在 [REST 协议](#rest-speech-recognition-api)中的行为可能与在 [WebSocket 协议](#webSocket-speech-recognition-api)中不同。 例如，REST API 不支持连续识别，即使在会话或听写模式下也是如此。
> [!NOTE]
> 直接使用 REST 或 WebSocket 协议时，这些模式适用。 [客户端库](GetStarted/GetStartedClientLibraries.md)使用不同的参数来指定识别模式。 有关详细信息，请参见你选择的客户端库。

Microsoft 语音服务为所有识别模式仅返回一个识别短语结果。 任何单个话语都有 15 秒的限制。

### <a name="interactive-mode"></a>交互模式

在 `interactive` 模式下，用户发出短请求并期望应用程序执行响应操作。

交互模式应用程序的典型特征如下：

- 用户知道自己正在对计算机说话，而不是对另一个人说话。
- 应用程序用户根据其希望应用程序执行的操作，提前知道他们想要说的内容。
- 话语通常持续约 2-3 秒。

### <a name="conversation-mode"></a>对话模式

在 `conversation` 模式中，用户参与人与人之间的对话。

对话模式应用程序的典型特征如下：

- 用户知道自己正在与另一个人交谈。
- 语音识别通过允许一个或两个参与者查看口述文本来增强人类对话。
- 用户并非始终计划他们想说的话。
- 用户常常使用俚语和其他非正式语言。

### <a name="dictation-mode"></a>听写模式

在 `dictation` 模式下，用户会向应用程序朗读更长的话语以进行进一步处理。

听写模式应用程序的典型特征如下：

- 用户知道自己正在对计算机说话。
- 用户能够看到语音识别文本结果。
- 用户通常会计划自己想说的内容并使用更为正式的语言。
- 用户朗读时长 5-8 秒的完整句子。

> [!NOTE]
> 在听写和会话模式下，Microsoft 语音服务不会返回部分结果。 相反，服务在音频流中的静默边界之后返回稳定的短语结果。 Microsoft 可能会增强语音协议，以改善这些连续识别模式下的用户体验。

## <a name="recognition-languages"></a>识别语言

识别语言指定应用程序用户所说的语言。 使用连接时的语言 URL 查询参数来指定识别语言。 语言查询参数的值使用 IETF 语言标记 [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag)，并且必须是语音识别 API 支持的语言之一。 可在[支持的语言](API-Reference-REST/supportedlanguages.md)页中找到语音服务支持语言的完整列表。

Microsoft 语音服务通过显示 `HTTP 400 Bad Request` 响应来拒绝无效的连接请求。 无效请求的特征为：

- 不包括语言查询参数值。
- 包括格式不正确的语言查询参数。
- 包括非受支持语言的语言查询参数。

可选择构建支持该服务支持的一种或所有语言的应用程序。

### <a name="example"></a>示例

在下方示例中，应用程序使用会话语音识别模式，适用语言为英语（美国）。

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>听录响应

听录响应将已转换的文本从音频返回给客户端。 听录响应包含以下字段：

- `RecognitionStatus` 指定识别的状态。 下表中给出了可能的值。

| 状态 | 说明 |
| ------------- | ---------------- |
| 成功 | 识别成功并且存在 DisplayText 字段 |
| NoMatch | 在音频流中检测到语音，但没有匹配目标语言的字词。 有关详细信息，请参阅 [NoMatch 识别状态 (#nomatch-recognition-status)  |
| InitialSilenceTimeout | 音频流的开始仅包含静音，并且服务在等待语音时超时 |
| BabbleTimeout | 音频流的开始仅包含噪音，并且服务在等待语音时超时 |
| 错误 | 识别服务遇到内部错误，无法继续 |

- `DisplayText` 表示在应用了大写、标点符号和反向文本规范化之后的已识别短语，并用星号屏蔽了不当字词。 仅当 `RecognitionStatus` 字段的值为 `Success` 时，才会显示 DisplayText 字段。

- `Offset` 指定识别出短语时的位置相对于音频流开头位置的偏移（以 100 纳秒为单位）。

- `Duration` 指定该语音短语的持续时间（以 100 纳秒为单位）。

如果需要，听录响应可返回更多信息。 若要了解如何返回更详细的输出，请参阅[输出格式](#output-format)。

Microsoft 语音服务支持其他听录过程，包括添加大写和标点符号、屏蔽不当字词以及将文本规范化为常见形式。 例如，如果用户说出短语“提醒我购买六部 iPhone”，Microsoft 语音服务将返回听录后的文本“提醒我购买 6 部 iPhone”。 将单词“六”转换为数字“6” 的过程称为反向文本规范化（简称 ITN）。

### <a name="nomatch-recognition-status"></a>NoMatch 识别状态

当 Microsoft 语音服务在音频流中检测到语音，但无法将该语音与正用于请求的语言语法匹配时，听录响应在 `RecognitionStatus` 中返回 `NoMatch`。 例如，当识别器将英语（美国）作为目标语言时，如果用户用德语讲话，则可能会出现 NoMatch 情况。 话语的波形模式能够指示存在人类语音，但没有说出任何字词将与识别器正使用的英语（美国）词典相匹配。

另外当识别算法找不到音频流中包含的声音的准确匹配时，也会出现 NoMatch 情况。 发生这种情况时，Microsoft 语音服务可能会生成包含假设文本的 speech.hypothesis 消息，但一定会生成 speech.phrase 消息，其中 RecognitionStatus 为 NoMatch。 这种情况很正常；不得对 speech.hypothesis 消息中文本的准确性或保真度做出任何假设。 此外，不得假设因为 Microsoft 语音服务生成 speech.hypothesis 消息，该服务就能够生成带有 RecognitionStatus 为 Success 的 speech.phrase 消息。

## <a name="output-format"></a>输出格式

Microsoft 语音服务可在听录响应中返回各种有效负载格式。 所有有效负载都采用 JSON 结构。

可通过指定 `format` URL 查询参数来控制短语结果格式。 默认情况下，该服务返回 `simple` 结果。

| 格式 | 说明 |
|-----|-----|
| `simple` | 简化的短语结果，包含识别状态和已识别文本的显示形式。 |
| `detailed` | 短语结果的识别状态和 N-best 列表，其中每个短语结果包含所有四种识别形式和置信度分数。 |

除了在响应中包含 `RecognitionStatus`、`Offset` 和 `duration` 外，`detailed` 格式还包含 [N-best 值](#n-best-values)。

### <a name="n-best-values"></a>N-best 值

无论是人还是计算机，任何收听者都无法确信自己听到的与他者表述的完全一致。 收听者只能向话语的特定解释分配一个概率。 

正常情况下，当人们与经常交往的其他人交谈时，有很大概率可以识别出其所说的字词。 基于计算机的语音侦听器努力达到类似的准确度水平，并且在适当的条件下[实现了与人类同等的水平](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v)。

在语音识别中使用的算法会探索话语的替代解释，这些替代解释作为正常处理的一部分。 当有十分有力的证据支持单一解释时，通常会放弃这些替代项。 但是，在不太理想的条件下，语音识别器在识别结束时会给出可能的备选解释列表。 此列表中的前 N 个替代项称为 N-best 列表。 每个替代项都分配有[置信度分数](#confidence)。 置信度分数范围为从 0 到 1。 得分为 1 表示最高置信度级别。 得分为 0 表示最低置信度级别。

> [!NOTE]
> 多个话语间 N-best 列表中的条目数有所不同。 同一话语的多次识别中的条目数可能不同。 这种变化是语音识别算法概率特性的自然而预期的结果。

N-best 列表中返回的每个条目都包含

- `Confidence`，表示此条目的[置信度分数](#confidence)。
- `Lexical`，它是已识别文本的[词法形式](#lexical-form)。
- `ITN`，它是已识别文本的 [ITN 形式](#itn-form)。
- `MaskedITN`，它是已识别文本的[已屏蔽的 ITN 形式](#masked-itn-form)。
- `Display`，它是已识别文本的[显示形式](#display-form)。

### 置信度分数 <a id="confidence"></a>

置信度分数是语音识别系统不可缺少的组成部分。 Microsoft 语音服务从置信度分类器获取置信度分数。 Microsoft 使用一组功能来训练置信度分类器，这些功能旨在最大程度地区分正确和错误的识别。 置信度分数的评估对象为单个字词和整个话语。

如果选择使用服务返回的置信度分数，请注意以下行为：

- 只能在使用相同的识别模式和语言的情况下比较置信度分数。 不要比较使用不同语言或不同识别模式得出的分数。 例如，交互识别模式中得出的置信度分数与听写模式中得出的置信度分数之间无相关性。
- 置信度分数最适用于一组数量有限的话语。 对于一组数量较多的话语，分数自然会发生较大程度的变化。

如果选择使用置信度分数值作为应用程序操作所依据的阈值，请使用语音识别来确立阈值。

- 对应用程序的代表性话语示例执行语音识别。
- 收集示例集中每个识别的置信度分数。
- 根据该示例的某些置信度百分点设置阈值。

没有任何单一阈值适用于所有应用程序。 对于某个应用程序来说可接受的置信度分数，对于其他应用程序来说可能是不可接受的。

### <a name="lexical-form"></a>词法形式

词法形式是话语中出现的原始识别文本，不含标点符号或大写。 例如，地址“1020 企业路” 的词法形式是“一零二零企业路”，假设如此表述。 句子“提醒我买 5 支铅笔” 的词法形式是“提醒我买五支铅笔”。

词法形式最适合需要执行非标准文本规范化的应用程序。 也适用于需要未经处理的识别字词的应用程序。

词法形式中的不当字词未屏蔽。

### <a name="itn-form"></a>ITN 形式

文本规范化是将文本从一种形式转换为另一种“规范”形式的过程。 例如，电话号码“555-1212”可能会被转换为规范形式“五五五一二一二”。 反向文本规范化 (ITN) 颠倒了这一过程，将单词“五五五一二一二”转换为反向规范形式“555-1212”。 识别结果的 ITN 形式不包括大写或标点符号。

ITN 形式最适合用于要处理已识别文本的应用程序。 例如，应用程序支持用户说出搜索词，并在 Web 查询中使用这些词，这种应用程序可能会使用 ITN 形式。 ITN 形式中的不当字词未屏蔽。 若要屏蔽不当字词，请使用已屏蔽的 ITN 形式。

### <a name="masked-itn-form"></a>已屏蔽的 ITN 形式

由于不当字词是口语的固有成分，因此 Microsoft 语音服务会识别所说话语中的这些字词和短语。 但是，不当字词可能不适用于所有应用程序，尤其是那些拥有受限的非成人用户受众的应用程序。

已屏蔽的 ITN 形式将不当字词屏蔽应用于反向文本规范化形式。 若要屏蔽不当字词，请将不当字词参数值的值设置为 `masked`。 不当字词屏蔽后，被识别为该语言的不当词汇部分的字词将被替换为星号。 例如：提醒我买 5 **** 铅笔。 识别结果的已屏蔽的 ITN 形式不包括大写或标点符号。

> [!NOTE]
> 如果不当字词查询参数值设置为 `raw`，则已屏蔽的 ITN 形式将与 ITN 形式相同。 不当字词未屏蔽。

### <a name="display-form"></a>显示形式

标点符号和大写用于表示强调和停顿位置等，使文本更易于理解。 显示形式向识别结果中添加标点符号和大写，使其成为最适用于显示语音文本的应用程序的形式。

由于显示形式扩展了已屏蔽的 ITN 形式，因此可将不当字词参数值设置为 `masked` 或 `raw`。 如果该值设置为 `raw`，则识别结果将包括用户说出的任何不当字词。 如果该值设置为 `masked`，被识别为该语言的不当词汇部分的字词将被替换为星号。

### <a name="sample-responses"></a>示例响应

所有有效负载都采用 JSON 结构。

`simple` 短语结果的有效负载格式：

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed` 短语结果的有效负载格式：

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>语音识别中的不当字词处理

Microsoft 语音服务识别所有形式的人类话语，包括许多人归类为“不当”的字词和短语。 可使用不当字词查询参数控制服务处理不当字词的方式。 默认情况下，该服务会屏蔽 speech.phrase 结果中的不当字词，并且不会返回包含不当字词的 speech.hypothesis 消息。

| 不当字词值 | 说明 |
| - | - |
| `masked` | 用星号屏蔽不当字词。 此选项为默认行为。 | 
| `removed` | 删除所有结果中的不当字词。 |
| `raw` | 识别并返回所有结果中的不当字词。 |

### <a name="profanity-value-masked"></a>不当字词值 `Masked`

若要屏蔽不当字词，请将不当字词查询参数设置为值“masked”。 当不当字词查询参数设为此值，或未为请求指定此参数时，服务会屏蔽不当字词。 服务通过用星号替换识别结果中的不当字词来执行屏蔽。 指定屏蔽不当字词时，服务不会返回包含不当字词的 speech.hypothesis 消息。

### <a name="profanity-value-removed"></a>不当字词值 `Removed`

当不当字词查询参数值设置为“removed”时，该服务将删除 speech.phrase 和 speech.hypothesis 消息中的不当字词。 结果中将不含任何不当字词。

#### <a name="profanity-only-utterances"></a>只含有不当字词的话语

当应用程序将服务配置为删除不当字词时，用户有可能只说了含有不当字词的话语。 对于这种情况，如果识别模式为听写或对话，则服务不会返回 speech.result。 如果识别模式为交互，则服务返回带有状态代码 NoMatch 的 speech.result。 

### <a name="profanity-value-raw"></a>不当字词值 `Raw`

当不当字词查询参数值设置为“raw”时，该服务将不会删除或屏蔽 speech.phrase 或 speech.hypothesis 消息中的不当字词。
