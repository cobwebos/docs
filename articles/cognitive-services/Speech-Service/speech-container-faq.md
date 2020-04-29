---
title: 语音服务容器常见问题（FAQ）
titleSuffix: Azure Cognitive Services
description: 安装和运行语音容器。 语音到文本转录音频流实时传输到文本，应用程序、工具或设备可以使用或显示这些内容。 文本转语音可将输入文本转换为类似人类的合成语音。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: aahi
ms.openlocfilehash: 17582244aef173da6ac700c980f7bd7fb0fec307
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81383085"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>语音服务容器常见问题（FAQ）

将语音服务与容器一起使用时，请在升级到支持之前依赖于这一系列的常见问题。 本文从总体到技术地捕获各种问题。 若要展开答案，请单击问题。

## <a name="general-questions"></a>一般问题

<details>
<summary>
<b>语音容器的工作原理以及如何对其进行设置？</b>
</summary>

**答案：** 设置生产群集时，需要考虑几个事项。 首先，在同一台计算机上设置单一语言、多个容器不应是一个大问题。 如果遇到问题，则可能是与硬件相关的问题，因此我们首先了解资源，即：CPU 和内存规范。

请考虑一下`ja-JP`容器和最新模型。 声音模型是最苛刻的 CPU，而语言模型则要求最大的内存。 当我们对使用进行了基准处理时，如果实时（如麦克风）流入音频，则需要大约 0.6 CPU 核心来处理单个语音到文本请求。 如果你比实时（如从文件）更快地将音频送进，则该使用情况可以为 double （1.2 x 核心）。 同时，下面列出的内存是用于解码语音的操作内存。 它不*会*考虑将驻留在文件缓存中的语言模型的实际完整大小。 对于`ja-JP`这一额外的 2 GB;对于`en-US`，它可能是更多（6-7 GB）。

如果有内存不足的计算机，并且您尝试在该计算机上部署多种语言，则文件缓存可能已满，并且操作系统会强制在页面模型中进出页面。对于正在运行的脚本，这可能会造成灾难性后果，并可能导致性能下降和其他性能影响。

此外，我们为具有[高级矢量扩展（AVX2）](speech-container-howto.md#advanced-vector-extension-support)指令集的计算机预先打包了可执行文件。 具有 AVX512 指令集的计算机将需要为该目标生成代码，而10个语言启动10个容器可能会暂时耗尽 CPU。 此类消息会出现在 docker 日志中：

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

最后，你可以使用`DECODER MAX_COUNT`变量在*单个*容器中设置所需的解码器数目。 基本上，我们应该从你的 SKU （CPU/内存）开始，我们可以建议如何充分利用它。 一个很好的起点是指推荐的主机资源规格。

<br>
</details>

<details>
<summary>
<b>你是否可以帮助进行容量规划和本地语音容器的成本估算？</b>
</summary>

**答案：** 对于批处理模式下的容器容量，每个解码器可以通过两个 CPU 内核（对于单个识别）实时处理 2-3 倍。 建议不要为每个容器实例保留两个以上的并发识别，但建议在负载均衡器后面运行更多的容器实例，以确保可靠性/可用性。

尽管我们可以让每个容器实例运行更多的解码器。 例如，可以在八个核心计算机上为每个容器实例设置7个解码器（每个实例超过2x 个），从而生成15x 吞吐量。 有一个需要注意`DECODER_MAX_COUNT`的参数。 在极端情况下，会出现可靠性和延迟问题，吞吐量会大幅增加。 对于麦克风，它将为1x 实时。 对于单个识别，总体使用量应为大约一个核心。

对于在批处理模式下处理 1 K 小时/天的情况，在极端情况下，3个 Vm 可以在24小时内处理它，但不能保证。 若要处理高峰天数、故障转移、更新和以提供最少的备份/BCP，我们建议4-5 计算机，而不是每个群集3个，并且有2个以上的群集。

对于硬件，我们使用标准 Azure VM `DS13_v2`作为参考（每个核心必须是 2.6 GHz 或更好的，且启用了 AVX2 指令集）。

| 实例  | vCPU （s） | RAM    | 临时存储 | AHB 的即用即付 | 1年准备金，含 AHB （节省了%） | 3年保留 AHB （节省百分比） |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $ 0.598/小时            | $ 0.3528/小时（约41%）                 | $ 0.2333/小时（约61%）                  |

基于设计参考（5个 Vm 的两个群集用于处理 1 K 小时/天的音频批处理），1年的硬件成本将是：

> 2（群集） * 5 （每个群集的 Vm） * $ 0.3528/小时 * 365 （天） * 24 （小时） = $ 31K/year

映射到物理计算机时，一般估算为 1 vCPU = 1 物理 CPU 内核。 事实上，1vCPU 比单核更强大。

对于本地，所有这些其他因素都将发挥作用：

- 物理 CPU 的类型和 it 上的内核数
- 同一盒/计算机上同时运行的 Cpu 数量
- 如何设置 Vm
- 如何使用超线程/多线程处理
- 如何共享内存
- 操作系统等。

通常情况下，它并不像 Azure 环境那样进行优化。 考虑到其他开销，我会说，安全估计是10个物理 CPU 内核 = 8 个 Azure vCPU。 尽管常用 Cpu 只包含八个核心。 对于本地部署，成本将高于使用 Azure Vm。 另外，请考虑折旧率。

服务成本与联机服务相同：语音到文本的 $ 1/小时。 语音服务成本为：

> $1 * 1000 * 365 = $ 365K

支付给 Microsoft 的维护费用取决于服务的服务级别和内容。 它的不同之处是，在涉及到现场服务时，将基本级别 $ 29.99/月份用于基本级别。 用于服务/维护的粗略数字是 $ 300/小时。 不包括人员成本。 不包括其他基础结构成本（例如存储、网络和负载均衡器）。

<br>
</details>

<details>
<summary>
<b>为什么脚本中缺少标点？</b>
</summary>

**答案：** 如果`speech_recognition_language=<YOUR_LANGUAGE>`使用的是碳客户端，应在请求中显式配置。

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
<b>能否在语音容器中使用自定义声音模型和语言模型？</b>
</summary>

目前只能传递一种模型 ID （自定义语言模型或自定义声音模型）。

**答案：** 决定*不*同时支持音频和语言模型。 这将保持有效，直到创建了统一的标识符以减少 API 中断。 遗憾的是，目前不支持这种情况。

<br>
</details>

<details>
<summary>
<b>你可以从自定义的语音到文本容器中解释这些错误吗？</b>
</summary>

**错误1：**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**答案1：** 如果你正在通过最新的自定义模型进行培训，我们目前不支持。 如果你使用较旧的版本进行训练，则应该可以使用。 我们仍在支持最新版本。

自定义容器本质上是不支持基于 Halide 或 ONNX 的声音模型（在自定义培训门户中是默认设置）。 这是因为自定义模型未进行加密，我们不想公开 ONNX 模型。语言模型是正确的。 客户需要为自定义培训显式选择一个较旧的非 ONNX 模型。 不会影响准确性。 模型大小可能大于（100 MB）。

> 支持模型 > 20190220 （版本4.5 统一）

**错误2：**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**答案2：** 需要在请求中提供正确的语音名称，这区分大小写。 请参阅完整的服务名称映射。 你必须使用`en-US-JessaRUS`，因为`en-US-JessaNeural`在文本到语音转换的容器版本中目前不可用。

**错误3：**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**答案3：** 您可以使用簧片来创建语音资源，而不是认知服务资源。


<br>
</details>

<details>
<summary>
<b>支持哪些 API 协议、REST 或 WS？</b>
</summary>

**答案：** 对于 "语音到文本" 和 "自定义语音到文本" 容器，我们目前仅支持基于 websocket 的协议。 SDK 仅支持在 WS 中调用，而不支持 REST 调用。 目前计划添加 REST 支持，而不是 ETA。 请始终参阅正式文档，请参阅[查询预测终结点](speech-container-howto.md#query-the-containers-prediction-endpoint)。

<br>
</details>

<details>
<summary>
<b>语音容器是否支持 CentOS？</b>
</summary>

**答案：** Python SDK 目前不支持 CentOS 7，还不支持 Ubuntu 19.04。

Python 语音 SDK 包适用于以下操作系统：
- **Windows** -x64 和 x86
- **Mac** -macOS X 版本10.12 或更高版本
- **Linux** -ubuntu 16.04、ubuntu 18.04、Debian 9 on x64

有关环境设置的详细信息，请参阅[Python 平台设置](quickstarts/setup-platform.md?pivots=programming-language-python)。 目前，Ubuntu 18.04 是推荐的版本。

<br>
</details>

<details>
<summary>
<b>为什么在尝试调用 LUIS 预测终结点时出现错误？</b>
</summary>

我正在 IoT Edge 部署中使用 LUIS 容器，并尝试从另一个容器调用 LUIS 预测终结点。 LUIS 容器正在侦听端口5001，使用的 URL 是：

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

我收到的错误是：

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

我在 LUIS 容器日志中看到请求，消息显示：

```cmd
The request path /luis//predict" does not match a supported file type.
```

这是什么意思呢？ 我丢失了什么？ 我从[这里](https://github.com/Azure-Samples/cognitive-services-speech-sdk)了解了语音 SDK 的示例。 方案是，我们正在直接从电脑麦克风检测音频，并根据我们训练的 LUIS 应用尝试确定目的。 我链接到的示例的确如此。 它适用于基于云的 LUIS 服务。 使用语音 SDK 似乎节省了对语音到文本 API 的单独显式调用，然后对 LUIS 进行了第二次调用。

因此，我要做的所有工作就是从在云中使用 LUIS 的方案中切换到使用 LUIS 容器。 我不能想象出语音 SDK 是否适用于另一种。

**答案：** 不应将语音 SDK 用于 LUIS 容器。 对于使用 LUIS 容器，应使用 LUIS SDK 或 LUIS REST API。 语音 SDK 应该用于语音容器。

云不同于容器。 一个云可以由多个聚合容器（有时称为微服务）组成。 因此，有一个 LUIS 容器，然后有一个语音容器-两个单独的容器。 语音容器只执行语音。 LUIS 容器只会 LUIS。 在云中，因为这两个容器都是已知的，并且对于远程客户端进入云、执行语音、返回、再次转到云并执行 LUIS，我们提供了一项功能，该功能允许客户端转到语音、离开云中，然后转到 LUIS 并返回到客户端。 这种情况下，即使在此方案中，语音 SDK 也会转到带有音频的语音云容器，然后语音云容器将与 LUIS cloud 容器进行通信。 LUIS 容器没有接受音频的概念（LUIS 容器接受流式处理音频（LUIS 是基于文本的服务）并没有什么意义。 使用本地时，我们不能保证我们的客户已部署两个容器，我们不会在我们的客户本地中的容器之间进行协调，并且如果这两个容器都部署在本地上（如果它们更本地到客户端），则不会负担首先回到客户端，让客户转到 LUIS。

<br>
</details>

<details>
<summary>
<b>为什么 macOS、语音容器和 Python SDK 出现错误？</b>
</summary>

当我们发送要转录的 *.wav*文件时，会返回以下结果：

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

我们知道 websocket 设置正确。

**答案：** 如果是这种情况，请参阅[此 GitHub 问题](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310)。 我们有一种解决方法，在此处进行了[建议](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)。

在版本1.8 上，碳固定。


<br>
</details>

<details>
<summary>
<b>语音容器终结点有何区别？</b>
</summary>

你是否能帮助填充以下测试指标，包括要测试的函数，以及如何测试 SDK 和 REST Api？ 特别是 "交互式" 和 "对话" 中的差异，我在现有的文档/示例中看不到这些差异。

| 终结点                                                | 功能测试                                                   | SDK 中 IsInRole 中的声明 | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | 合成文本（文本到语音转换）                                  |     | 是      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | 认知服务本地听写 v1 websocket 终结点        | 是 | 否       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | 认知服务本地交互式 v1 websocket 终结点  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | 认知服务本地会话 v1 websocket 终结点 |     |          |

**答案：** 这是以下内容的合成：
- 尝试将听写终结点用于容器的人员（我不确定其如何获取该 URL）
- 1个<sup>st</sup>方终结点是容器中的终结点。
- 1个<sup>st</sup>方终结点返回了 speech 消息，而不`speech.hypothesis`是消息中的3个<sup>rd</sup>部件终结点返回的消息。
- "碳快速入门" `RecognizeOnce`全部使用（交互模式）
- 使用带有断言的，而`speech.fragment`要求它们的消息不会以交互模式返回。
- 在发布版本中，将断言触发（终止进程）。

解决方法是在代码中切换为使用连续识别，或者（更快）连接到容器中的交互式或连续终结点。
对于你的代码，请将终结点设置为 <主机： port>/speech/recognition/interactive/cognitiveservices/v1

有关各种模式，请参阅语音模式-请参阅以下内容：

[!INCLUDE [speech-modes](includes/speech-modes.md)]

正确的修补程序随附了 SDK 1.8，后者具有本地支持（将选择正确的终结点，因此，我们不会比联机服务更糟）。 同时，还提供了持续识别的示例，为什么不指向它呢？

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>对于各种音频文件，我应该使用哪种模式？</b>
</summary>

**答案：** 下面是[使用 Python 的快速入门](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python)教程。 您可以在文档网站上找到其他所链接的语言。

只是为了阐明交互式、对话和听写;这是一个高级方法，用于指定服务处理语音请求的特定方式。 遗憾的是，对于本地容器，我们必须指定完整的 URI （因为它包括本地计算机），因此此信息泄露自抽象。 我们正在与 SDK 团队合作，使其更易于使用。

<br>
</details>

<details>
<summary>
<b>如何对事务/秒/内核的大致度量值进行基准测试？</b>
</summary>

**答案：** 下面是一些从现有模型获得的粗略数字（在我们将在 GA 中交付的产品中，会更好地变化）：

- 对于文件，限制将在语音 SDK 的2倍。 不限制前5秒的音频。 解码器能够完成3倍的实时操作。 为此，单个识别的总体 CPU 使用率将接近2个内核。
- 对于 mic，它将为1x 实时。 对于单个识别，总体使用量应大约为1个核心。

可以通过 docker 日志来验证这一点。 我们实际上会转储包含 session 和短语/查询文本统计信息的行，其中包含 RTF 数字。


<br>
</details>

<details>
<summary>
<b>将音频文件拆分为语音容器使用的 chucks 是很常见的吗？</b>
</summary>

我当前的计划是使用现有的音频文件并将其拆分为10秒的块，并通过容器发送这些块。 这是一种可接受的方案？  是否有更好的方法来处理容器的更大的音频文件？

**答案：** 只需使用 speech SDK 并为其指定文件，它就会执行正确的操作。 为什么需要将文件分块？


<br>
</details>

<details>
<summary>
<b>如何实现在同一主机上运行多个容器？</b>
</summary>

Doc 指出要公开其他端口，但 LUIS 容器仍在侦听端口5000？

**答案：** 尝试`-p <outside_unique_port>:5000`。 例如，`-p 5001:5000` 。


<br>
</details>

## <a name="technical-questions"></a>技术问题

<details>
<summary>
<b>如何使非批处理 Api 长时间处理音频&lt;？</b>
</summary>

**答：** `RecognizeOnce()`在交互模式下，仅处理最多15秒的音频，因为模式适用于语音命令，其中最谈话应为 short。 如果使用`StartContinuousRecognition()`听写或会话，则不会限制15秒。


<br>
</details>

<details>
<summary>
<b>建议使用哪些资源、CPU 和 RAM;对于50并发请求？</b>
</summary>

有多少个并发请求将有4核，4 GB RAM 句柄？ 例如，如果我们必须提供50的并发请求，建议使用多少个核心和 RAM？

**答案：** 与我们的最新`en-US`信息，我们建议使用超过6个并发请求的更多 docker 容器。 它获取的 crazier 超过16个内核，并将其变成非一致性内存访问（NUMA）节点。 下表描述了每个语音容器的最小和建议的资源分配。

# <a name="speech-to-text"></a>[语音到文本](#tab/stt)

| 容器      | 最小值             | 建议         |
|----------------|---------------------|---------------------|
| 语音转文本 | 2核，2 GB 内存 | 4核，4 GB 内存 |

# <a name="custom-speech-to-text"></a>[自定义语音到文本](#tab/cstt)

| 容器             | 最小值             | 建议         |
|-----------------------|---------------------|---------------------|
| 自定义语音转文本 | 2核，2 GB 内存 | 4核，4 GB 内存 |

# <a name="text-to-speech"></a>[文本到语音转换](#tab/tts)

| 容器      | 最小值             | 建议         |
|----------------|---------------------|---------------------|
| 文本转语音 | 单核，2-GB 内存 | 2核，3 GB 内存 |

# <a name="custom-text-to-speech"></a>[自定义文本到语音转换](#tab/ctts)

| 容器             | 最小值             | 建议         |
|-----------------------|---------------------|---------------------|
| 自定义文本到语音转换 | 单核，2-GB 内存 | 2核，3 GB 内存 |

***

- 每个核心都必须至少为 2.6 GHz 或更快。
- 对于文件，限制将在语音 SDK 中，以2倍速（不限制音频的前5秒）。
- 解码器可以完成大约2到3倍的实时工作。 为此，单个识别的总体 CPU 使用率将接近两个内核。 这就是我们不建议为每个容器实例保留两个以上的活动连接的原因。 极端情况是在八核计算机（如`DS13_V2`）中将大约10个解码器置于8个核心计算机上。 对于容器版本1.3 及更高版本，可以尝试设置`DECODER_MAX_COUNT=20`一个参数。
- 对于麦克风，它将为1x 实时。 对于单个识别，总体使用量应为大约一个核心。

请考虑音频的总小时数。 如果数字太大，则为了提高可靠性/可用性，我们建议在负载平衡器后面的一个或多个框中运行多个容器实例。 可以使用 Kubernetes （K8S）和 Helm 或使用 Docker 撰写来完成业务流程。

例如，若要处理1000小时/24 小时，我们已尝试设置3-4 个 Vm，每个 VM 有10个实例/解码器。

<br>
</details>

<details>
<summary>
<b>语音容器是否支持标点符号？</b>
</summary>

**答案：** 在本地容器中提供了大写（故障）。 标点依赖于语言，不支持某些语言，包括中文和日语。

对于现有容器，我们*确实*支持隐式和基本标点，但默认情况`off`下为。 这意味着你可以获取示例中的`.`字符，但不能获取`。`字符。 若要启用此隐式逻辑，请参阅下面的示例，了解如何使用我们的语音 SDK 在 Python 中执行此操作（与其他语言类似）：

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
<b>尝试将数据发布到语音到文本容器时，为什么会收到404错误？</b>
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

**答案：** 我们不支持在 "语音到文本" 容器中 REST API，仅通过语音 SDK 支持 Websocket。 请始终参阅正式文档，请参阅[查询预测终结点](speech-container-howto.md#query-the-containers-prediction-endpoint)。

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

**答案：** 当音频的进比语音识别容器可以获得的速度快时，通常会发生这种情况。 将填充客户端缓冲区，并触发取消。 需要控制用于发送音频的并发性和 RTF。

<br>
</details>

<details>
<summary>
<b>您是否可以从 c + + 示例解释这些文本到语音容器的错误？</b>
</summary>

**答案：** 如果容器版本早于1.3，则应使用以下代码：

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

旧容器没有用于使用`FromHost` API 的所需终结点。 如果容器用于版本1.3，则应使用以下代码：

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

下面是使用`FromEndpoint` API 的示例：

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 由于`SetSpeechSynthesisVoiceName`具有更新的文本到语音引擎的容器需要语音名称，因此调用函数。

<br>
</details>

<details>
<summary>
<b>如何将用于 speech SDK 的版本1.7 用于语音容器？</b>
</summary>

**答案：** 语音容器上有三个终结点用于不同的用法，它们被定义为语音模式-请参阅以下内容：

[!INCLUDE [speech-modes](includes/speech-modes.md)]

它们用于不同的目的，并且使用方式不同。

Python[示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)：
- 对于带有自定义终结点的单个识别（交互模式）（即`SpeechConfig`对于终结点参数），请`speech_recognize_once_from_file_with_custom_endpoint_parameters()`参见。
- 对于连续识别（对话模式），只需修改才能使用上述自定义终结点，请`speech_recognize_continuous_from_file()`参阅。
- 若要在上面的示例中启用听写（仅在你确实需要时），请在`speech_config`创建之后， `speech_config.enable_dictation()`添加代码。

在 c # 中，若要启用`SpeechConfig.EnableDictation()`听写，请调用函数。

### <a name="fromendpoint-apis"></a>`FromEndpoint`Api
| 语言 | API 详细信息 |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 目前不受支持，也不计划。 |

<br>
</details>

<details>
<summary>
<b>如何使用带有语音容器的语音 SDK 的1.8 版？</b>
</summary>

**答案：** 有一个新`FromHost`的 API。 这不会替换或修改任何现有的 Api。 它只是添加了使用自定义主机创建语音配置的替代方法。

### <a name="fromhost-apis"></a>`FromHost`Api

| 语言 | API 详细信息 |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | 目前不支持 |

> 参数：主机（必需）、订阅密钥（可选，如果你可以使用不带它的服务）。

Host 的格式为`protocol://hostname:port` ， `:port`其中是可选的（请参阅下文）：
- 如果容器在本地运行，则主机名为`localhost`。
- 如果容器在远程服务器上运行，则使用该服务器的主机名或 IPv4 地址。

用于语音到文本的主机参数示例：
- `ws://localhost:5000`-使用端口5000连接到本地容器
- `ws://some.host.com:5000`-与远程服务器上运行的容器的连接不安全

上面的 Python 示例，但使用`host`参数而不`endpoint`是：

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [认知服务容器](speech-container-howto.md)
