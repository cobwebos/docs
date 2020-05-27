---
title: 安装语音容器-语音服务
titleSuffix: Azure Cognitive Services
description: 安装和运行语音容器。 语音转文本可将音频流实时听录为应用程序、工具或设备可以使用或显示的文本。 文本转语音可将输入文本转换为类似人类的合成语音。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.openlocfilehash: 1d4fde8dd21911b70d5a1c0f3b23304a3468a2a6
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816227"
---
# <a name="install-and-run-speech-service-containers-preview"></a>安装和运行语音服务容器（预览）

容器使你能够在自己的环境中运行一些语音服务 Api。 容器非常适合于特定的安全和数据管理要求。 本文介绍如何下载、安装和运行语音容器。

语音容器使客户能够构建一个语音应用程序体系结构，该体系结构针对强健的云功能和边缘区域进行了优化。 可使用四个不同的容器。 这两个标准容器为**语音到文本**和**文本到语音转换**。 这两个自定义容器是**自定义语音文本**和**自定义文本到语音转换**。 语音容器与基于云的 Azure 语音服务具有相同的[定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 所有语音容器当前均作为[公共 "封闭" 预览版](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)的一部分提供。 语音容器推出正式发行版（GA）时将发出一则通知。

| 函数 | 功能 | 最新 |
|--|--|--|
| 语音转文本 | 利用中间结果分析情绪和转录连续实时语音或批处理音频记录。  | 2.2.0 |
| 自定义语音转文本 | 使用[自定义语音门户](https://speech.microsoft.com/customspeech)中的自定义模型，转录连续实时语音或批处理音频记录到带有中间结果的文本中。 | 2.2.0 |
| 文本转语音 | 用纯文本输入或语音合成标记语言（SSML）将文本转换为自然声音语音。 | 1.4.0 |
| 自定义文本到语音转换 | 使用[自定义语音门户](https://aka.ms/custom-voice-portal)中的自定义模型，使用纯文本输入或语音合成标记语言（SSML）将文本转换为自然声音声音。 | 1.4.0 |

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

使用语音容器之前的先决条件如下：

| 必选 | 目的 |
|--|--|
| Docker 引擎 | 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> **在 Windows 上**，还必须将 Docker 配置为支持 Linux 容器。<br><br> |
| 熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。 |
| 语音资源 | 若要使用这些容器，必须具有：<br><br>一个用于获取关联 API 密钥和终结点 URI 的 Azure 语音资源。__ 可在 Azure 门户的**语音**“概述”和“密钥”页上获取两个值。 必须获取这两个值才能启动容器。<br><br>**{API_KEY}**： "**密钥**" 页上有两个可用的资源键之一<br><br>**{ENDPOINT_URI}**： "**概述**" 页中提供的终结点 |

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

填写并提交[请求窗体](https://aka.ms/cognitivegate)，请求对容器的访问权限。 


[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

**** 主机是运行 docker 容器的计算机。 主机*必须支持*[高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)（AVX2）。 可以通过以下命令在 Linux 主机上检查 AVX2 支持：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> *需要*主机计算机才能支持 AVX2。 如果不支持 AVX2，容器*将无法*正常运行。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了每个语音容器的最小和建议的资源分配。

# <a name="speech-to-text"></a>[语音转文本](#tab/stt)

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 语音转文本 | 2核，2 GB 内存 | 4核，4 GB 内存 |

# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 自定义语音转文本 | 2核，2 GB 内存 | 4核，4 GB 内存 |

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 文本转语音 | 单核，2-GB 内存 | 2核，3 GB 内存 |

# <a name="custom-text-to-speech"></a>[自定义文本到语音转换](#tab/ctts)

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 自定义文本到语音转换 | 单核，2-GB 内存 | 2核，3 GB 内存 |

***

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

> [!NOTE]
> 最小和推荐的基于 Docker 限制，而*不*是主机资源。 例如，"语音到文本" 容器内存映射大型语言模型的部分，并*建议*将整个文件放在内存中，这是额外的 4-6 GB。 另外，由于模型要在内存中分页，因此首次运行任一容器可能需要更长的时间。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

以下容器注册表中提供了语音容器图像。

# <a name="speech-to-text"></a>[语音转文本](#tab/stt)

| 容器 | 存储库 |
|-----------|------------|
| 语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

| 容器 | 存储库 |
|-----------|------------|
| 自定义语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

| 容器 | 存储库 |
|-----------|------------|
| 文本转语音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[自定义文本到语音转换](#tab/ctts)

| 容器 | 存储库 |
|-----------|------------|
| 自定义文本到语音转换 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>用于语音容器的 Docker 拉取

# <a name="speech-to-text"></a>[语音转文本](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>用于语音到文本的容器的 Docker 拉取

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> `latest`标记将提取 `en-US` 区域设置。 有关其他区域设置，请参阅[语音到文本区域设置](#speech-to-text-locales)。

#### <a name="speech-to-text-locales"></a>语音到文本区域设置

除之外的所有标记 `latest` 均采用以下格式，并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

以下标记是格式示例：

```
2.2.0-amd64-en-us-preview
```

对于 "**语音到文本**" 容器的所有受支持的区域设置，请参阅[语音到文本图像标记](../containers/container-image-tags.md#speech-to-text)。

# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>自定义语音到文本的容器的 Docker 拉取

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale` `voice` 自定义语音容器的和由容器的自定义模型引入确定。

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>文本到语音容器的 Docker 拉取

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 标记提取 `en-US` 区域设置和 `jessarus` 语音。 有关其他区域设置，请参阅[文本到语音的区域设置](#text-to-speech-locales)。

#### <a name="text-to-speech-locales"></a>文本到语音的区域设置

除之外的所有标记 `latest` 均采用以下格式，并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

以下标记是格式示例：

```
1.3.0-amd64-en-us-jessarus-preview
```

对于支持的**文本到语音转换**容器的所有区域设置和相应声音，请参阅[文本到语音图像标记](../containers/container-image-tags.md#text-to-speech)。

> [!IMPORTANT]
> 构造*标准文本到语音转换*HTTP POST 时，[语音合成标记语言（SSML）](speech-synthesis-markup.md)消息需要 `voice` 具有属性的元素 `name` 。 该值是对应的容器区域设置和语音，也称为["短名称"](language-support.md#standard-voices)。 例如，标记的 `latest` 语音名称将为 `en-US-JessaRUS` 。

# <a name="custom-text-to-speech"></a>[自定义文本到语音转换](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>自定义文本到语音容器的 Docker 拉取

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale` `voice` 自定义语音容器的和由容器的自定义模型引入确定。

***

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](speech-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{Endpoint_URI}` 和 `{API_Key}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 还提供了命令的其他[示例](speech-container-configuration.md#example-docker-run-commands) `docker run` 。

# <a name="speech-to-text"></a>[语音转文本](#tab/stt)

若要运行*语音到文本*的容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行 "*语音到文本*" 容器。
* 分配4个 CPU 核心和4千兆字节（GB）的内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>分析语音到文本输出的情绪 

从 "语音到文本" 容器的 v 2.2.0 开始，可以对输出调用[情绪分析 V3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) 。 若要调用情绪分析，需要文本分析 API 资源终结点。 例如： 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

如果要访问云中的文本分析终结点，你将需要一个密钥。 如果在本地运行文本分析，则可能不需要提供此。

密钥和终结点作为参数传递到语音容器，如下面的示例中所示。

```bash
docker run -it --rm -p 5000:5000 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

此命令：

* 执行与上述命令相同的步骤。
* 存储用于发送情绪分析请求的文本分析 API 终结点和密钥。 


# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

*自定义语音到文本*的容器依赖于自定义语音模型。 自定义模型必须使用[自定义语音门户](https://speech.microsoft.com/customspeech)进行[训练](how-to-custom-speech-train-model.md)。

> [!IMPORTANT]
> 需要从以下模型版本之一训练自定义语音模型：
> * **20181201（v 3.3 统一）**
> * **20190520（v 4.14 统一）**
> * **20190701（v 4.17 统一）**<br>
> ![自定义语音训练容器模型](media/custom-speech/custom-speech-train-model-container-scoped.png)

运行容器需要自定义语音**模型 ID** 。 可在自定义语音门户的 "**培训**" 页找到此功能。 在自定义语音门户中，导航到**定型**页面并选择模型。
<br>

![自定义语音培训页](media/custom-speech/custom-speech-model-training.png)

获取用于作为命令参数的参数的**模型 ID** `ModelId` `docker run` 。
<br>

![自定义语音模型详细信息](media/custom-speech/custom-speech-model-details.png)

下表表示各种 `docker run` 参数及其相应的说明：

| 参数 | 说明 |
|---------|---------|
| `{VOLUME_MOUNT}` | 宿主计算机的[卷装载](https://docs.docker.com/storage/volumes/)，docker 使用它来持久保存自定义模型。 例如， *C:\CustomSpeech* ，其中*C 驱动器*位于主机上。 |
| `{MODEL_ID}` | 自定义语音门户的 "**培训**" 页中的自定义语音**模型 ID** 。 |
| `{ENDPOINT_URI}` | 此终结点对于计量和计费是必需的。 有关详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 |
| `{API_KEY}` | API 密钥是必需的。 有关详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 |

若要运行*自定义语音到文本*的容器，请执行以下 `docker run` 命令：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行*自定义语音到文本*的容器。
* 分配4个 CPU 核心和4千兆字节（GB）的内存。
* 从卷输入装载中加载*自定义语音到文本*模型，例如*C:\CustomSpeech*。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 在给定的 `ModelId` 情况（如果在卷装载上找不到）中下载模型。
* 如果先前已下载自定义模型， `ModelId` 则将忽略。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="text-to-speech"></a>[文本转语音](#tab/tts)

若要运行*文本到语音*的容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像中运行*文本到语音*容器。
* 分配2个 CPU 核心和1千兆字节（GB）的内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="custom-text-to-speech"></a>[自定义文本到语音转换](#tab/ctts)

自定义的*文本到语音转换*容器依赖于自定义语音模型。 自定义模型必须使用[自定义语音门户](https://aka.ms/custom-voice-portal)进行[训练](how-to-custom-voice-create-voice.md)。 需要自定义语音**模型 ID**才能运行容器。 可在自定义语音门户的 "**培训**" 页中找到。 在自定义语音门户中，导航到**定型**页面并选择模型。
<br>

![自定义语音培训页](media/custom-voice/custom-voice-model-training.png)

获取要用作 docker run 命令的参数参数的**模型 ID** `ModelId` 。
<br>

![自定义语音模型详细信息](media/custom-voice/custom-voice-model-details.png)

下表表示各种 `docker run` 参数及其相应的说明：

| 参数 | 说明 |
|---------|---------|
| `{VOLUME_MOUNT}` | 宿主计算机的[卷装载](https://docs.docker.com/storage/volumes/)，docker 使用它来持久保存自定义模型。 例如， *C:\CustomSpeech* ，其中*C 驱动器*位于主机上。 |
| `{MODEL_ID}` | 自定义语音门户的 "**培训**" 页中的自定义语音**模型 ID** 。 |
| `{ENDPOINT_URI}` | 此终结点对于计量和计费是必需的。 有关详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 |
| `{API_KEY}` | API 密钥是必需的。 有关详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 |

若要运行*自定义的文本到语音转换*容器，请执行以下 `docker run` 命令：

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行*自定义的文本到语音转换*容器。
* 分配2个 CPU 核心和1千兆字节（GB）的内存。
* 从卷输入装载中加载*自定义的文本到语音转换*模型，例如*C:\CustomVoice*。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 在给定的 `ModelId` 情况（如果在卷装载上找不到）中下载模型。
* 如果先前已下载自定义模型， `ModelId` 则将忽略。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

***

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

> [!NOTE]
> 如果要运行多个容器，请使用唯一端口号。

| 容器 | SDK 主机 URL | 协议 |
|--|--|--|
| 语音到文本和自定义语音到文本 | `ws://localhost:5000` | WS |
| 文本到语音转换和自定义文本到语音转换 | `http://localhost:5000` | HTTP |

有关使用 WSS 和 HTTPS 协议的详细信息，请参阅[容器安全性](../cognitive-services-container-support.md#azure-cognitive-services-container-security)。

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>分析情绪

如果[为容器](#analyze-sentiment-on-the-speech-to-text-output)提供了文本分析 API 凭据，则可以使用语音 SDK 通过情绪分析发送语音识别请求。 可以将 API 响应配置为使用*简单*或*详细*格式。

# <a name="simple-format"></a>[简单格式](#tab/simple-format)

若要将语音客户端配置为使用简单格式，请将添加 `"Sentiment"` 为的值 `Simple.Extensions` 。 如果要选择特定的文本分析模型版本，请 `'latest'` 在 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` 属性配置中替换。

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions`将返回响应的根层中的情绪结果。

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[详细格式](#tab/detailed-format)

若要将语音客户端配置为使用详细格式，请将添加 `"Sentiment"` 为 `Detailed.Extensions` 、 `Detailed.Options` 或的值。 如果要选择特定的文本分析模型版本，请 `'latest'` 在 `speechcontext-phraseDetection.sentimentAnalysis.modelversion` 属性配置中替换。

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions`提供响应根层中的情绪结果。 `Detailed.Options`提供 `NBest` 响应层的结果。 它们可以单独使用，也可以一起使用。

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

如果要完全禁用情绪分析，请将值添加 `false` 到 `sentimentanalysis.enabled` 。

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-or-custom-text-to-speech"></a>文本到语音转换或自定义文本到语音转换

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

若要使用公开端口运行多个容器，请确保在运行每个容器时使用不同的公开端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

可以让此容器和其他 Azure 认知服务容器一起运行在该主机上。 此外，还可以让同一认知服务容器的多个容器一起运行。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

启动或运行容器时，可能会遇到问题。 使用输出[装载](speech-container-configuration.md#mount-settings)并启用日志记录。 这样，容器就可以生成日志文件，这些文件在解决问题时非常有用。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

语音容器使用 Azure 帐户中的“语音”资源向 Azure 发送计费信息。**

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](speech-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

本文已介绍语音容器的概念，及其下载、安装和运行工作流。 综上所述：

* 语音为 Docker 提供四个 Linux 容器，封装各种功能：
  * *语音转文本*
  * *自定义语音转文本*
  * *文本转语音*
  * *自定义文本到语音转换*
* 容器映像从 Azure 中的容器注册表下载。
* 容器映像在 Docker 中运行。
* 指定容器的主机 URI 时，是使用 REST API （仅文本到语音转换）还是 SDK （语音转换文本或文本到语音转换）。 
* 实例化容器时，必须提供计费信息。

> [!IMPORTANT]
>  如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看配置设置的[容器](speech-container-configuration.md)
* 了解如何[结合使用 Speech service 容器和 Kubernetes 和 Helm](speech-container-howto-on-premises.md)
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
