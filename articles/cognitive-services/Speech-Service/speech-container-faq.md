---
title: 语音服务容器常见问题 （FAQ）
titleSuffix: Azure Cognitive Services
description: 安装和运行语音容器。 语音到文本将音频流实时转录到文本，应用程序、工具或设备可以使用或显示。 文本转语音可将输入文本转换为类似人类的合成语音。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9b0fd89693517bdb63ba6f4265fddf2b0aa57ba5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874477"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>语音服务容器常见问题 （FAQ）

将语音服务与容器一起使用时，请依赖此常见问题集合，然后再升级以支持。 本文从一般到技术，都不同程度地捕获了问题。 要展开答案，请单击该问题。

## <a name="general-questions"></a>一般问题

<details>
<summary>
<b>语音容器如何工作，如何设置它们？</b>
</summary>

**答案：** 设置生产群集时，需要考虑以下几点。 首先，在同一台计算机上设置单一语言、多个容器不应是大问题。 如果您遇到问题，这可能是一个与硬件相关的问题 - 因此，我们将首先查看资源，即;CPU 和内存规格。

考虑一下，`ja-JP`容器和最新的模型。 声学模型是 CPU 要求最高的部分，而语言模型需要最多的内存。 当我们对使用进行基准测试时，当音频实时流入时（如从麦克风流出），大约需要 0.6 个 CPU 内核来处理单个语音到文本请求。 如果要以比实时（如从文件中）更快地馈送音频，则该使用量可以加倍（1.2 倍内核）。 同时，下面列出的内存是用于解码语音的操作内存。 它不考虑*语言*模型的实际全大小，该大小将驻留在文件缓存中。 对于`ja-JP`这是额外的2 GB;对于`en-US`，它可能更多 （6-7 GB）。

如果计算机内存不足，并且尝试在计算机上部署多种语言，则文件缓存可能已满，并且操作系统被迫将模型页到和外页。对于正在运行的转录，这可能是灾难性的，并可能导致减速和其他性能影响。

此外，我们为具有[高级矢量扩展 （AVX2）](speech-container-howto.md#advanced-vector-extension-support)指令集的计算机预包装可执行文件。 具有 AVX512 指令集的计算机将需要为该目标生成代码，并且为 10 种语言启动 10 个容器可能会暂时耗尽 CPU。 像这样的消息将显示在 docker 日志中：

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

最后，您可以使用`DECODER MAX_COUNT`变量设置*单个容器内*所需的解码器数量。 因此，基本上，我们应该从您的 SKU（CPU/内存）开始，我们可以建议如何充分利用它。 一个很好的起点是参考推荐的主机资源规格。

<br>
</details>

<details>
<summary>
<b>您能否帮助预置语音容器的容量规划和成本估算？</b>
</summary>

**答案：** 对于批处理模式下的容器容量，每个解码器可以实时处理 2-3 倍，并带有两个 CPU 内核，实现单个识别。 我们不建议每个容器实例保留两个以上并发识别，但出于可靠性/可用性原因，建议在负载均衡器后面运行更多的容器实例。

尽管我们可以让每个容器实例使用更多的解码器运行。 例如，我们可以在 8 台核心计算机上为每个容器实例设置 7 个解码器（每台代码数超过 2 倍），从而产生 15 倍的吞吐量。 需要注意一个参数`DECODER_MAX_COUNT`。 对于极端情况，会出现可靠性和延迟问题，吞吐量显著增加。 对于麦克风，它将是 1 倍的实时。 对于单个识别，总体用法应约为一个核心。

对于批处理模式下每天处理 1 K 小时的情况，在极端情况下，3 个 VM 可以在 24 小时内处理，但不能保证。 要处理高峰日、故障转移、更新和提供最少的备份/BCP，我们建议使用 4-5 台计算机，而不是每个群集的 3 台计算机，以及 2+ 群集。

对于硬件，我们使用标准 Azure `DS13_v2` VM 作为参考（每个内核必须为 2.6 GHz 或更高，启用 AVX2 指令集）。

| 实例  | vCPU | RAM    | 临时存储 | 随付随付 | 带 AHB 的 1 年期准备金（百分比储蓄） | 3年预留与AHB（百分比节省） |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 吉布 | 112 GiB      | $0.598/小时            | $0.3528/小时 （+41%）                 | $0.2333/小时 （+61%）                  |

基于设计参考（两个 5 个 VM 群集可处理 1 K 小时/天音频批处理），1 年硬件成本为：

> 2（群集） = 5 （每个群集的 VM） = 0.3528 美元/小时 = 365 美元（天） = 24 （小时） = 31K / 年

映射到物理计算机时，一般估计为 1 vCPU = 1 物理 CPU 核心。 实际上，1vCPU 比单个内核更强大。

对于预赛，所有这些附加因素都起作用了：

- 物理 CPU 的类型及其上有多少个内核
- 在同一盒/机上一起运行的 CPU 数量
- 如何设置 VM
- 如何使用超线程/多线程
- 如何共享内存
- 操作系统等

通常，它不像 Azure 环境那样调整良好。 考虑到其他开销，我认为安全估计是 10 个物理 CPU 内核 = 8 个 Azure vCPU。 虽然流行的 CPU 只有八个内核。 使用预部署时，成本将高于使用 Azure VM。 此外，请考虑折旧率。

服务成本与在线服务相同：语音到文本每小时 1 美元。 语音服务成本为：

> $1 = 1000 × 365 = $365K

支付给 Microsoft 的维护成本取决于服务的服务级别和服务内容。 从29.99美元/月的基本水平到几十万，如果现场服务涉及。 粗略的数字是300美元/小时的服务/维护。 不包括人员成本。 不包括其他基础结构成本（如存储、网络和负载均衡器）。

<br>
</details>

<details>
<summary>
<b>为什么转录中缺少标点符号？</b>
</summary>

**答案：** 如果`speech_recognition_language=<YOUR_LANGUAGE>`请求中使用碳客户端，则应在请求中显式配置 。

例如：

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
输出如下：

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>是否可以将自定义声学模型和语言模型与语音容器一起使用？</b>
</summary>

我们目前只能传递一个模型 ID，无论是自定义语言模型还是自定义声学模型。

**答案：** 决定*同时不支持声学*和语言模型。 这将保持有效，直到创建统一标识符以减少 API 中断。 因此，不幸的是，现在不支持这一点。

<br>
</details>

<details>
<summary>
<b>您能否从自定义语音到文本容器解释这些错误？</b>
</summary>

**错误 1：**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**答案 1：** 如果您正在培训最新的自定义模型，我们目前不支持。 如果使用旧版本进行训练，则应可以使用。 我们仍在努力支持最新版本。

从本质上讲，自定义容器不支持基于 Halide 或 ONNX 的声学模型（这是自定义培训门户中的默认值）。 这是由于自定义模型未加密，我们不想公开 ONNX 模型;但是，我们不希望公开 ONNX 模型。语言模型很好。 客户需要显式选择较旧的非 ONNX 模型进行自定义培训。 准确性不会受到影响。 型号大小可能更大（100 MB）。

> 支持型号 > 20190220 （v4.5 统一）

**错误 2：**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**答案 2：** 您需要在请求中提供正确的语音名称，该名称区分大小写。 请参阅完整的服务名称映射。 您必须使用`en-US-JessaRUS`，因为`en-US-JessaNeural`现在在文本到语音转换的容器版本中不可用。

**错误 3：**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**答案 3：** 您可以重新创建语音资源，而不是认知服务资源。


<br>
</details>

<details>
<summary>
<b>支持哪些 API 协议、REST 或 WS？</b>
</summary>

**答案：** 对于语音到文本和自定义语音到文本容器，我们目前仅支持基于 Websocket 的协议。 SDK 仅支持在 WS 中调用，而不支持 REST。 有一个计划添加 REST 支持，但暂时没有 ETA。 始终参考官方文档，请参阅[查询预测终结点](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>

<details>
<summary>
<b>语音容器是否支持 CentOS？</b>
</summary>

**答案：** CentOS 7 尚不受 Python SDK 支持，也不支持 Ubuntu 19.04。

Python 语音 SDK 包适用于以下操作系统：
- **视窗**- x64 和 x86
- **Mac** - macOS X 版本 10.12 或更高版本
- **Linux** - Ubuntu 16.04， 乌本图 18.04， Debian 9 在 x64

有关环境设置的详细信息，请参阅[Python 平台设置](quickstarts/setup-platform.md?pivots=programming-language-python)。 目前，Ubuntu 18.04 是推荐的版本。

<br>
</details>

<details>
<summary>
<b>为什么在尝试调用 LUIS 预测终结点时会收到错误？</b>
</summary>

我在 IoT Edge 部署中使用 LUIS 容器，并尝试从其他容器调用 LUIS 预测终结点。 LUIS 容器正在侦听端口 5001，我使用的 URL 是：

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

我得到的错误是：

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

我在 LUIS 容器日志中看到请求，消息显示：

```cmd
The request path /luis//predict" does not match a supported file type.
```

这是什么意思呢？ 我错过了什么？ 我在此关注语音 SDK[的示例。](https://github.com/Azure-Samples/cognitive-services-speech-sdk) 场景是，我们直接从 PC 麦克风检测音频，并尝试根据我们培训的 LUIS 应用程序确定意图。 我链接到的示例正是这样做的。 它与 LUIS 基于云的服务配合良好。 使用语音 SDK 似乎使我们不必对语音到文本 API 进行单独的显式调用，然后对 LUIS 进行第二次调用。

因此，我尝试所做的只是从在云中使用 LUIS 的方案切换到使用 LUIS 容器。 我无法想象，如果语音 SDK 适用于其中一个，它将不会适用于另一个。

**答案：** 不应对 LUIS 容器使用语音 SDK。 对于使用 LUIS 容器，应使用 LUIS SDK 或 LUIS REST API。 语音 SDK 应针对语音容器使用。

云与容器不同。 云可以由多个聚合容器（有时称为微服务）组成。 因此，有一个LUIS容器，然后有一个语音容器 - 两个单独的容器。 "语音"容器仅执行语音。 LUIS 容器仅执行 LUIS。 在云中，由于已知两个容器都已部署，并且远程客户端进入云、做语音、返回，然后再次访问云并做 LUIS，因此我们提供了一个功能，允许客户端转到语音、留在云中、转到 LUIS 然后返回客户端。 因此，即使在这种情况下，语音 SDK 也会使用音频转到语音云容器，然后语音云容器与 LUIS 云容器进行文本对话。 LUIS 容器没有接受音频的概念（LUIS 容器接受流式音频没有意义 - LUIS 是基于文本的服务）。 对于 Prem，我们无法确定我们的客户已经部署了两个容器，我们假定在客户处所的容器之间进行协调，如果两个容器都部署在 prem 上，因为它们对于客户来说更加本地，那么先去 SR、返回客户端、让客户获取该文本并转到 LUIS 并不是一种负担。

<br>
</details>

<details>
<summary>
<b>为什么我们收到 macOS、语音容器和 Python SDK 的错误？</b>
</summary>

当我们发送要转录的 *.wav*文件时，结果会返回：

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

我们知道网络套接字设置正确。

**答案：** 如果是这种情况，请参阅[此 GitHub 问题](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)。 我们有一个解决方法，[建议在这里](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)。

碳在版本 1.8 上修复了此点。


<br>
</details>

<details>
<summary>
<b>语音容器终结点中有哪些差异？</b>
</summary>

您能否帮助填写以下测试指标，包括要测试哪些功能以及如何测试 SDK 和 REST API？ 特别是"交互"和"对话"的差异，我没有看到从现有的文档/示例。

| 终结点                                                | 功能测试                                                   | SDK 中 IsInRole 中的声明 | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | 合成文本（文本到语音）                                  |     | 是      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | 认知服务预听写 v1 Websocket 终结点        | 是 | 否       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | 认知服务上预交互式 v1 Websocket 终结点  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | 预处理会话 v1 Websocket 终结点上的认知服务 |     |          |

**答案：** 这是融合：
- 尝试容器的听写终结点的人（我不确定他们是如何获得该 URL 的）
- <sup>第</sup>1 个端终结点是容器中的终结点。
- 第<sup>1</sup>方终结点返回语音.片段消息，而不是`speech.hypothesis`<sup>第</sup>3 部分终结点返回的消息，用于听写终结点。
- 碳快速启动所有使用`RecognizeOnce`（交互模式）
- 碳断言，对于`speech.fragment`需要它们不以交互模式返回的消息。
- 在释放中断言火的碳（扼杀过程）。

解决方法是切换到在代码中使用连续识别，或者（更快地）连接到容器中的交互式或连续终结点。
对于代码，将终结点设置为<主机：端口>/语音/识别/交互式/认知服务/v1

有关各种模式，请参阅语音模式 - 请参阅以下内容：

[!INCLUDE [speech-modes](includes/speech-modes.md)]

正确的修复程序是带有 SDK 1.8，它有预支持（将选择正确的终结点，所以我们不会比在线服务差）。 同时，还有一个连续识别的样本，我们为什么不指出呢？

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>我应该为各种音频文件使用哪种模式？</b>
</summary>

**答案：** 下面是[使用 Python 的快速入门](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python)。 您可以在文档网站上找到链接的其他语言。

只是为了澄清互动、对话和听写;这是指定我们的服务处理语音请求的特定方式的一种高级方法。 遗憾的是，对于 prem 容器，我们必须指定完整的 URI（因为它包括本地计算机），因此此信息从抽象中泄漏。 我们正在与 SDK 团队合作，使将来更加可用。

<br>
</details>

<details>
<summary>
<b>我们如何对交易/秒/核的粗略衡量基准？</b>
</summary>

**答案：** 以下是一些对现有模型的预期粗略数字（将在 GA 中发货的模型中为更好的数字而改变）：

- 对于文件，限制将在语音 SDK 中，为 2 倍。 前五秒的音频不会受到限制。 解码器能够实时执行大约 3 倍的时间。 为此，单个识别的总体 CPU 使用率将接近 2 个内核。
- 对于麦克风，它将在 1 倍的实时。 单个识别的总体用法应约为 1 核。

这一切都可以从 Docker 日志中验证。 实际上，我们使用会话和短语/陈述统计信息转储行，其中包括 RTF 编号。


<br>
</details>

<details>
<summary>
<b>将音频文件拆分为夹头以用于语音容器使用是否常见？</b>
</summary>

我目前的计划是获取一个现有的音频文件，并将其拆分为 10 秒的块，并通过容器发送这些块。 这是可接受的方案吗？  是否有更好的方法来处理更大的音频文件与容器？

**答案：** 只需使用语音 SDK 并给它文件，它将做正确的事情。 为什么需要分块文件？


<br>
</details>

<details>
<summary>
<b>如何使多个容器在同一主机上运行？</b>
</summary>

文档说要公开一个不同的端口，我这样做，但 LUIS 容器仍在侦听端口 5000？

**答案：** 尝试`-p <outside_unique_port>:5000`。 例如，`-p 5001:5000` 。


<br>
</details>

## <a name="technical-questions"></a>技术问题

<details>
<summary>
<b>如何获取非批处理 API 来处理 15&lt;秒长的音频？</b>
</summary>

**答案：** 这是交互式模式。 如果您使用听写或对话，则不是问题。


<br>
</details>

<details>
<summary>
<b>推荐的资源、CPU 和 RAM 是什么;对于 50 个并发请求？</b>
</summary>

4 核 4 GB RAM 句柄有多少个并发请求？ 例如，如果我们必须提供 50 个并发请求，建议多少个核心请求和 RAM？

**答案：** 实时，8与我们的最新`en-US`，所以我们建议使用更多的 docker 容器超过 6 个并发请求。 它变得疯狂超过16个内核，并成为非统一的内存访问（NUMA）节点敏感。 下表描述了每个语音容器的最小和建议的资源分配。

# <a name="speech-to-text"></a>[语音转文本](#tab/stt)

| 容器      | 最小值             | 建议         |
|----------------|---------------------|---------------------|
| 语音转文本 | 2 核，2 GB 内存 | 4 核，4 GB内存 |

# <a name="custom-speech-to-text"></a>[自定义语音到文本](#tab/cstt)

| 容器             | 最小值             | 建议         |
|-----------------------|---------------------|---------------------|
| 自定义语音转文本 | 2 核，2 GB 内存 | 4 核，4 GB内存 |

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

| 容器      | 最小值             | 建议         |
|----------------|---------------------|---------------------|
| 文本转语音 | 单核，2-GB 内存 | 2 核，3 GB内存 |

# <a name="custom-text-to-speech"></a>[自定义文本到语音](#tab/ctts)

| 容器             | 最小值             | 建议         |
|-----------------------|---------------------|---------------------|
| 自定义文本到语音 | 单核，2-GB 内存 | 2 核，3 GB内存 |

***

- 每个内核必须至少为 2.6 GHz 或更快。
- 对于文件，限制将在语音 SDK 中，为 2x（前 5 秒音频不受限制）。
- 解码器能够实时执行大约 2-3 倍。 为此，对于单个识别，总 CPU 使用率将接近两个内核。 这就是为什么我们不建议每个容器实例保留两个以上活动连接。 极端的一面是把大约10解码器在2倍的实时在8核机器像`DS13_V2`。 对于容器版本 1.3 和更高版本，您可以尝试设置`DECODER_MAX_COUNT=20`参数。
- 对于麦克风，它将是 1 倍的实时。 对于单个识别，总体用法应约为一个核心。

考虑您拥有的总音频小时数。 如果数量很大，为了提高可靠性/可用性，我们建议在负载平衡器后面运行更多容器实例，无论是在单个框中还是在多个框中。 业务流程可以使用 Kubernetes （K8S） 和 Helm 完成，也可以使用 Docker 撰写。

例如，为了处理 1000 小时/24 小时，我们尝试设置 3-4 个 VM，每个 VM 包含 10 个实例/解码器。

<br>
</details>

<details>
<summary>
<b>语音容器是否支持标点符号？</b>
</summary>

**答案：** 我们在前置容器中提供了大写 （ITN）。 标点符号依赖于语言，某些语言（包括中文和日语）不支持。

对于现有容器，*我们确实有*隐式和基本标点符号支持，但默认情况下是`off`默认的。 这意味着你可以得到你的例子中的`.`字符，但不能得到`。`字符。 为了启用此隐式逻辑，下面是如何使用我们的语音 SDK 在 Python 中执行此操作的示例（在其他语言中，它类似）：

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>为什么在尝试将 POST 数据到语音到文本容器时出现 404 个错误？</b>
</summary>

下面是一个示例 HTTP POST：

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**答案：** 我们不支持在语音到文本容器中的 REST API，我们仅通过语音 SDK 支持 WebSocket。 始终参考官方文档，请参阅[查询预测终结点](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>

<details>
<summary>
<b>使用语音到文本服务时，为什么会收到此错误？</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**答案：** 当您以比"语音识别"容器所获取的更快速度馈送音频时，通常会发生这种情况。 客户端缓冲区已满，并触发取消。 您需要控制发送音频的并发和 RTF。

<br>
</details>

<details>
<summary>
<b>您能否从C++示例中解释这些文本到语音容器错误？</b>
</summary>

**答案：** 如果容器版本早于 1.3，则应使用此代码：

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

较旧的容器没有碳处理`FromHost`API 所需的终结点。 如果用于版本 1.3 的容器，则应使用此代码：

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

下面是使用`FromEndpoint`API 的示例：

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 调用`SetSpeechSynthesisVoiceName`该函数是因为具有更新的文本到语音转换引擎的容器需要语音名称。

<br>
</details>

<details>
<summary>
<b>如何使用带有语音容器的语音 SDK v1.7？</b>
</summary>

**答案：** 对于不同的用法，语音容器上有三个终结点，它们被定义为语音模式 - 如下所示：

[!INCLUDE [speech-modes](includes/speech-modes.md)]

它们用于不同的目的，并且使用方式不同。

Python[示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)：
- 对于具有自定义终结点的单识别（交互式模式）（即;`SpeechConfig`使用终结点参数），请参阅`speech_recognize_once_from_file_with_custom_endpoint_parameters()`。
- 对于连续识别（对话模式），只需修改以使用上述自定义终结点，请参阅`speech_recognize_continuous_from_file()`。
- 要在上述示例中启用听写（仅在您真正需要时），在创建`speech_config`后，请添加代码`speech_config.enable_dictation()`。

在 C# 中启用听写，请`SpeechConfig.EnableDictation()`调用函数。

### <a name="fromendpoint-apis"></a>`FromEndpoint`Api
| 语言 | API 详细信息 |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initWithEndpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 当前不支持，也不计划。 |

<br>
</details>

<details>
<summary>
<b>如何使用带有语音容器的语音 SDK v1.8？</b>
</summary>

**答案：** 有一个新的`FromHost`API。 这不会替换或修改任何现有的 API。 它只是添加了使用自定义主机创建语音配置的替代方法。

### <a name="fromhost-apis"></a>`FromHost`Api

| 语言 | API 详细信息 |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 目前不支持 |

> 参数：主机（必需）、订阅密钥（可选，如果无需使用服务即可）。

主机格式是`protocol://hostname:port`可选的`:port`（见下文）：
- 如果容器在本地运行，则主机名为`localhost`。
- 如果容器在远程服务器上运行，请使用该服务器的主机名或 IPv4 地址。

语音到文本的主机参数示例：
- `ws://localhost:5000`- 使用端口 5000 与本地容器的非安全连接
- `ws://some.host.com:5000`- 与远程服务器上运行的容器的非安全连接

Python 示例来自上面，但`host`使用参数而不是`endpoint`：

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [认知服务容器](speech-container-howto.md)
