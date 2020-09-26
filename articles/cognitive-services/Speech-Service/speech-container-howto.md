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
ms.date: 09/24/2020
ms.author: aahi
ms.openlocfilehash: be4b7f6a0f8afae9188b1f969956424041e77d9e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371671"
---
# <a name="install-and-run-speech-service-containers"></a>安装和运行语音服务容器 

通过容器，你可以在自己的环境中运行一些语音服务 API。 容器非常适合用于满足特定的安全性和数据管理要求。 本文介绍如何下载、安装以及运行语音容器。

通过语音容器，客户能够生成语音应用程序体系结构，该体系结构针对强大的云功能和边缘位置进行了优化。 有几个容器可用，它们使用的 [定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 与基于云的 Azure 语音服务相同。


> [!IMPORTANT]
> 以下语音容器现已正式发布：
> * 标准语音到文本
> * 自定义语音转文本
> * 标准文本到语音转换
> 
> 以下语音容器处于封闭预览。
> * 自定义文本到语音转换
> * 语音语言检测 
> * 神经文本到语音转换
>
> 若要使用语音容器，你必须提交联机请求并获得批准。 有关详细信息，请参阅下面的 " **运行容器的请求批准"** 部分。

| 容器 | 功能 | 最新 |
|--|--|--|
| 语音转文本 | 利用中间结果分析情绪和转录连续实时语音或批处理音频记录。  | 2.5.0 |
| 自定义语音转文本 | 使用 [自定义语音门户](https://speech.microsoft.com/customspeech)中的自定义模型，转录连续实时语音或批处理音频记录到带有中间结果的文本中。 | 2.5.0 |
| 文本转语音 |  (SSML) ，以纯文本输入或语音合成标记语言将文本转换为自然声音。 | 1.7.0 |
| 自定义文本到语音转换 | 使用 [自定义语音门户](https://aka.ms/custom-voice-portal)中的自定义模型，使用纯文本输入或语音合成标记语言 (SSML) 将文本转换为自然声音。 | 1.7.0 |
| 语音语言检测 | 检测音频文件中的语言。 | 1.0 |
| 神经文本到语音转换 | 使用 deep 神经网络技术将文本转换为自然声音，允许使用更自然的合成语音。 | 1.2.0 |

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

使用语音容器之前的先决条件如下：

| 必须 | 目的 |
|--|--|
| Docker 引擎 | 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> **** 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br> |
| 熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。 |
| 语音资源 | 若要使用这些容器，必须具有：<br><br>一个用于获取关联 API 密钥和终结点 URI 的 Azure 语音资源。__ 可在 Azure 门户的**语音**“概述”和“密钥”页上获取两个值。 必须获取这两个值才能启动容器。<br><br>**{API_KEY}** ：“密钥”页上提供的两个可用资源密钥中的一个****<br><br>**{ENDPOINT_URI}** ：“概述”页上提供的终结点**** |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

**** 主机是运行 docker 容器的计算机。 主机 *必须支持* (AVX2) 的 [高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) 。 可使用以下命令检查 Linux 主机是否提供 AVX2 支持：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> 需要** 主计算机来支持 AVX2。 如果没有 AVX2 支持，容器将无法** 正常运行。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了每个语音容器的最小和建议的资源分配。

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 语音转文本 | 2核，2 GB 内存 | 4核，4 GB 内存 |
| 自定义语音转文本 | 2核，2 GB 内存 | 4核，4 GB 内存 |
| 文本转语音 | 单核，2-GB 内存 | 2核，3 GB 内存 |
| 自定义文本到语音转换 | 单核，2-GB 内存 | 2核，3 GB 内存 |
| 语音语言检测 | 1个内核，1 GB 内存 | 1个内核，1 GB 内存 |
| 神经文本到语音转换 | 6核，12 GB 内存 | 8核，16 GB 内存 |

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

> [!NOTE]
> 最小和推荐的基于 Docker 限制，而 *不* 是主机资源。 例如，"语音到文本" 容器内存映射大型语言模型的部分，并 *建议* 将整个文件放在内存中，这是额外的 4-6 GB。 另外，由于模型要在内存中分页，因此首次运行任一容器可能需要更长的时间。

## <a name="request-approval-to-the-run-the-container"></a>请求批准运行容器

填写并提交 [请求窗体](https://aka.ms/csgate) ，请求对容器的访问权限。 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

以下容器注册表中提供了语音容器图像。

# <a name="speech-to-text"></a>[语音到文本](#tab/stt)

| 容器 | 存储库 |
|-----------|------------|
| 语音转文本 | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

| 容器 | 存储库 |
|-----------|------------|
| 自定义语音转文本 | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[文本到语音转换](#tab/tts)

| 容器 | 存储库 |
|-----------|------------|
| 文本转语音 | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[神经文本到语音转换](#tab/ntts)

| 容器 | 存储库 |
|-----------|------------|
| 神经文本到语音转换 | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[自定义文本到语音转换](#tab/ctts)

| 容器 | 存储库 |
|-----------|------------|
| 自定义文本到语音转换 | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[语音语言检测](#tab/lid)

| 容器 | 存储库 |
|-----------|------------|
| 语音语言检测 | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>用于语音容器的 Docker 拉取

# <a name="speech-to-text"></a>[语音到文本](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>用于语音到文本的容器的 Docker 拉取

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从容器预览注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> `latest`标记将提取 `en-US` 区域设置。 有关其他区域设置，请参阅 [语音到文本区域设置](#speech-to-text-locales)。

#### <a name="speech-to-text-locales"></a>语音到文本区域设置

除之外的所有标记 `latest` 均采用以下格式，并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

以下标记是格式示例：

```
2.4.0-amd64-en-us-preview
```

对于 " **语音到文本** " 容器的所有受支持的区域设置，请参阅 [语音到文本图像标记](../containers/container-image-tags.md#speech-to-text)。

# <a name="custom-speech-to-text"></a>[自定义语音转文本](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>自定义语音到文本的容器的 Docker 拉取

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从容器预览注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> `locale` `voice` 自定义语音容器的和由容器的自定义模型引入确定。

# <a name="text-to-speech"></a>[文本到语音转换](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>文本到语音容器的 Docker 拉取

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从容器预览注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 标记提取 `en-US` 区域设置和 `ariarus` 语音。 有关其他区域设置，请参阅 [文本到语音的区域设置](#text-to-speech-locales)。

#### <a name="text-to-speech-locales"></a>文本到语音的区域设置

除之外的所有标记 `latest` 均采用以下格式，并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

以下标记是格式示例：

```
1.6.0-amd64-en-us-ariarus-preview
```

对于支持的 **文本到语音转换** 容器的所有区域设置和相应声音，请参阅 [文本到语音图像标记](../containers/container-image-tags.md#text-to-speech)。

> [!IMPORTANT]
> 构造 *文本到语音转换* HTTP POST 时， [ (SSML) 消息的语音合成标记语言 ](speech-synthesis-markup.md) 需要 `voice` 具有属性的元素 `name` 。 该值是对应的容器区域设置和语音，也称为 ["短名称"](language-support.md#standard-voices)。 例如，标记的 `latest` 语音名称将为 `en-US-AriaRUS` 。

# <a name="neural-text-to-speech"></a>[神经文本到语音转换](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>用于神经文本到语音转换容器的 Docker 请求

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从容器预览注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 标记提取 `en-US` 区域设置和 `arianeural` 语音。 有关其他区域设置，请参阅 [神经文本到语音的区域设置](#neural-text-to-speech-locales)。

#### <a name="neural-text-to-speech-locales"></a>神经文本到语音区域设置

除之外的所有标记 `latest` 均采用以下格式，并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

以下标记是格式示例：

```
1.2.0-amd64-en-us-arianeural-preview
```

对于所有受支持的区域设置以及 **神经文本到语音转换** 容器的相应声音，请参阅 [神经文本到语音图像标记](../containers/container-image-tags.md#neural-text-to-speech)。

> [!IMPORTANT]
> 构造 *神经文本到语音转换* HTTP POST 时， [ (SSML) 消息的语音合成标记语言 ](speech-synthesis-markup.md) 需要 `voice` 具有属性的元素 `name` 。 该值是对应的容器区域设置和语音，也称为 ["短名称"](language-support.md#neural-voices)。 例如，标记的 `latest` 语音名称将为 `en-US-AriaNeural` 。

# <a name="custom-text-to-speech"></a>[自定义文本到语音转换](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>自定义文本到语音容器的 Docker 拉取

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从容器预览注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> `locale` `voice` 自定义语音容器的和由容器的自定义模型引入确定。

# <a name="speech-language-detection"></a>[语音语言检测](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>适用于 Speech 语言检测容器的 Docker 拉取

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从容器预览注册表下载容器映像。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](speech-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{Endpoint_URI}` 和 `{API_Key}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。 还提供了命令的其他 [示例](speech-container-configuration.md#example-docker-run-commands) `docker run` 。

# <a name="speech-to-text"></a>[语音到文本](#tab/stt)

若要运行标准的 *语音到文本* 的容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行 " *语音到文本* " 容器。
* 分配4个 CPU 核心和 4 gb (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>分析语音到文本输出的情绪 

从 "语音到文本" 容器的 v 2.2.0 开始，可以对输出调用 [情绪分析 V3 API](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) 。 若要调用情绪分析，需要文本分析 API 资源终结点。 例如： 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

如果要访问云中的文本分析终结点，你将需要一个密钥。 如果在本地运行文本分析，则可能不需要提供此。

密钥和终结点作为参数传递到语音容器，如下面的示例中所示。

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
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
> * **20181201 (v 3.3 统一) **
> * **20190520 (4.14 统一) **
> * **20190701 (4.17 统一) **<br>
> ![自定义语音训练容器模型](media/custom-speech/custom-speech-train-model-container-scoped.png)

运行容器需要自定义语音 **模型 ID** 。 可在自定义语音门户的 " **培训** " 页找到此功能。 在自定义语音门户中，导航到 **定型** 页面并选择模型。
<br>

![自定义语音培训页](media/custom-speech/custom-speech-model-training.png)

获取用于作为命令参数的参数的 **模型 ID** `ModelId` `docker run` 。
<br>

![自定义语音模型详细信息](media/custom-speech/custom-speech-model-details.png)

下表表示各种 `docker run` 参数及其相应的说明：

| 参数 | 说明 |
|---------|---------|
| `{VOLUME_MOUNT}` | 宿主计算机的 [卷装载](https://docs.docker.com/storage/volumes/)，docker 使用它来持久保存自定义模型。 例如， *C:\CustomSpeech* ，其中 *C 驱动器* 位于主机上。 |
| `{MODEL_ID}` | 自定义语音门户的 "**培训**" 页中的自定义语音**模型 ID** 。 |
| `{ENDPOINT_URI}` | 此终结点对于计量和计费是必需的。 有关详细信息，请参阅 [收集所需的参数](#gathering-required-parameters)。 |
| `{API_KEY}` | API 密钥是必需的。 有关详细信息，请参阅 [收集所需的参数](#gathering-required-parameters)。 |

若要运行 *自定义语音到文本* 的容器，请执行以下 `docker run` 命令：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行 *自定义语音到文本* 的容器。
* 分配4个 CPU 核心和 4 gb (GB) 内存。
* 从卷输入装载中加载 *自定义语音到文本* 模型，例如 *C:\CustomSpeech*。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* `ModelId`如果在卷装入) 上找不到，则下载给定 (的模型。
* 如果先前已下载自定义模型， `ModelId` 则将忽略。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="text-to-speech"></a>[文本到语音转换](#tab/tts)

若要运行标准 *文本到语音转换* 容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行标准的 *文本到语音转换* 容器。
* 分配1个 CPU 核心和 2 gb (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="neural-text-to-speech"></a>[神经文本到语音转换](#tab/ntts)

若要运行 *神经文本到语音的* 容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行 *神经文本到语音* 容器。
* 分配6个 CPU 核心和 12 gb (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="custom-text-to-speech"></a>[自定义文本到语音转换](#tab/ctts)

自定义的 *文本到语音转换* 容器依赖于自定义语音模型。 自定义模型必须使用[自定义语音门户](https://aka.ms/custom-voice-portal)进行[训练](how-to-custom-voice-create-voice.md)。 需要自定义语音 **模型 ID** 才能运行容器。 可在自定义语音门户的 " **培训** " 页中找到。 在自定义语音门户中，导航到 **定型** 页面并选择模型。
<br>

![自定义语音培训页](media/custom-voice/custom-voice-model-training.png)

获取要用作 docker run 命令的参数参数的 **模型 ID** `ModelId` 。
<br>

![自定义语音模型详细信息](media/custom-voice/custom-voice-model-details.png)

下表表示各种 `docker run` 参数及其相应的说明：

| 参数 | 说明 |
|---------|---------|
| `{VOLUME_MOUNT}` | 宿主计算机的 [卷装载](https://docs.docker.com/storage/volumes/)，docker 使用它来持久保存自定义模型。 例如， *C:\CustomSpeech* ，其中 *C 驱动器* 位于主机上。 |
| `{MODEL_ID}` | 自定义语音门户的 "**培训**" 页中的自定义语音**模型 ID** 。 |
| `{ENDPOINT_URI}` | 此终结点对于计量和计费是必需的。 有关详细信息，请参阅 [收集所需的参数](#gathering-required-parameters)。 |
| `{API_KEY}` | API 密钥是必需的。 有关详细信息，请参阅 [收集所需的参数](#gathering-required-parameters)。 |

若要运行 *自定义的文本到语音转换* 容器，请执行以下 `docker run` 命令：

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行 *自定义的文本到语音转换* 容器。
* 分配1个 CPU 核心和 2 gb (GB) 内存。
* 从卷输入装载中加载 *自定义的文本到语音转换* 模型，例如 *C:\CustomVoice*。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* `ModelId`如果在卷装入) 上找不到，则下载给定 (的模型。
* 如果先前已下载自定义模型， `ModelId` 则将忽略。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="speech-language-detection"></a>[语音语言检测](#tab/lid)

若要运行 *Speech 语言检测* 容器，请执行以下 `docker run` 命令。

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令： 

* 从容器映像运行语音语言检测容器。 目前，你不需要支付运行此映像的费用。 
* 分配1个 CPU 核心和 1 gb (GB) 内存。
* 公开 TCP 端口5003，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

如果要使用 "语音到文本" 容器运行此容器，则可以使用此 [Docker 映像](https://hub.docker.com/r/antsu/on-prem-client)。 在两个容器都启动后，使用此 Docker Run 命令执行 `speech-to-text-with-languagedetection-client` 。

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> 增加并发调用的数量可能会影响可靠性和延迟。 对于语言检测，建议最多使用1个 CPU 和1GB 内存的并发调用数。 对于具有2个 Cpu 和 2 GB 内存的主机，建议最多6个并发调用。

***

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

> [!NOTE]
> 如果要运行多个容器，请使用唯一端口号。

| 容器 | SDK 主机 URL | 协议 |
|--|--|--|
| 标准语音到文本和自定义语音到文本 | `ws://localhost:5000` | WS |
| 文本到语音 (包括标准、自定义和神经) 、语音语言检测 | `http://localhost:5000` | HTTP |

有关使用 WSS 和 HTTPS 协议的详细信息，请参阅 [容器安全性](../cognitive-services-container-support.md#azure-cognitive-services-container-security)。

### <a name="speech-to-text-standard-and-custom"></a>语音到文本 (标准和自定义) 

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>分析情绪

如果 [为容器](#analyze-sentiment-on-the-speech-to-text-output)提供了文本分析 API 凭据，则可以使用语音 SDK 通过情绪分析发送语音识别请求。 可以将 API 响应配置为使用 *简单* 或 *详细* 格式。
> [!NOTE]
> 适用于 Speech Service Python SDK 的 v2.0 已识别情绪分析问题。 如果使用的是语音服务 Python SDK 中的情绪分析，请使用 v 1.12. x 或更早版本。

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

`Simple.Extensions` 将返回响应的根层中的情绪结果。

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

`Detailed.Extensions` 提供响应根层中的情绪结果。 `Detailed.Options` 提供 `NBest` 响应层的结果。 它们可以单独使用，也可以一起使用。

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

### <a name="text-to-speech-standard-neural-and-custom"></a>文本到语音 (标准、神经和自定义) 

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

若要使用公开端口运行多个容器，请确保在运行每个容器时使用不同的公开端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

可以让此容器和其他 Azure 认知服务容器一起运行在该主机上。 此外，还可以让同一认知服务容器的多个容器一起运行。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

启动或运行容器时，可能会遇到问题。 使用输出 [装载](speech-container-configuration.md#mount-settings) 并启用日志记录。 这样，容器就可以生成日志文件，这些文件在解决问题时非常有用。

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
  * *语音到文本*
  * *自定义语音转文本*
  * *文本到语音转换*
  * *自定义文本到语音转换*
  * *神经文本到语音转换*
  * *语音语言检测*
* 容器映像从 Azure 中的容器注册表下载。
* 容器映像在 Docker 中运行。
* 使用 REST API 仅 (文本到语音) 或 SDK (语音到文本或文本到语音，) 指定容器的主机 URI。 
* 实例化容器时，必须提供计费信息。

> [!IMPORTANT]
>  如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看配置设置的[容器](speech-container-configuration.md)
* 了解如何 [结合使用 Speech service 容器和 Kubernetes 和 Helm](speech-container-howto-on-premises.md)
* 使用更多 [认知服务容器](../cognitive-services-container-support.md)
