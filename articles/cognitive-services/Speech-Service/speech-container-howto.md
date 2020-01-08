---
title: 安装语音容器-语音服务
titleSuffix: Azure Cognitive Services
description: 安装并运行语音容器。 语音转文本可将音频流实时听录为应用程序、工具或设备可以使用或显示的文本。 文本转语音可将输入文本转换为类似人类的合成语音。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: dapine
ms.openlocfilehash: d5ecc104c7845a1881cbcdecfbccb75148f6e070
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "74815355"
---
# <a name="install-and-run-speech-service-containers-preview"></a>安装和运行语音服务容器（预览）

容器使你能够在自己的环境中运行一些语音服务 Api。 容器非常适合于特定的安全和数据管理要求。 本文介绍如何下载、安装和运行语音容器。

语音容器使客户能够构建一个语音应用程序体系结构，该体系结构针对强健的云功能和边缘区域进行了优化。 可使用四个不同的容器。 这两个标准容器为**语音到文本**和**文本到语音转换**。 这两个自定义容器是**自定义语音文本**和**自定义文本到语音转换**。

> [!IMPORTANT]
> 所有语音容器当前均作为[公共 "封闭" 预览版](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)的一部分提供。 语音容器推出正式发行版（GA）时将发出一则通知。

| 函数 | 功能 | 最新 |
|--|--|--|
| 语音转文本 | 转录连续实时语音或批处理音频记录到带有中间结果的文本中。 | 2.0.0 |
| 自定义语音到文本 | 使用[自定义语音门户](https://speech.microsoft.com/customspeech)中的自定义模型，转录连续实时语音或批处理音频记录到带有中间结果的文本中。 | 2.0.0 |
| 文本转语音 | 用纯文本输入或语音合成标记语言（SSML）将文本转换为自然声音语音。 | 1.3.0 |
| 自定义文本到语音转换 | 使用[自定义语音门户](https://aka.ms/custom-voice-portal)中的自定义模型，使用纯文本输入或语音合成标记语言（SSML）将文本转换为自然声音声音。 | 1.3.0 |

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

使用语音容器之前的先决条件如下：

| 需要 | 用途 |
|--|--|
| Docker 引擎 | 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br> |
| 熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。 |
| 语音资源 | 若要使用这些容器，必须具有：<br><br>用于获取关联的 API 密钥和终结点 URI 的 Azure_语音_资源。 Azure 门户的 "**语音**概述" 和 "密钥" 页上都有这两个值。 它们都是启动容器所必需的。<br><br>**{API_KEY}** ： "**密钥**" 页上有两个可用的资源键之一<br><br>**{ENDPOINT_URI}** ： "**概述**" 页中提供的终结点 |

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

填写并提交[认知服务语音容器请求窗体](https://aka.ms/speechcontainerspreview/)，请求对容器的访问权限。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

主机是运行 docker 容器的计算机。 主机*必须支持*[高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)（AVX2）。 可以通过以下命令在 Linux 主机上检查 AVX2 支持：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> *需要*主机计算机才能支持 AVX2。 如果不支持 AVX2，容器*将无法*正常运行。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了每个语音容器的最小和建议的资源分配。

# <a name="speech-to-texttabstt"></a>[语音转文本](#tab/stt)

| 容器 | 最小值 | 推荐 |
|-----------|---------|-------------|
| 语音转文本 | 2核，2 GB 内存 | 4核，4 GB 内存 |

# <a name="custom-speech-to-texttabcstt"></a>[自定义语音到文本](#tab/cstt)

| 容器 | 最小值 | 推荐 |
|-----------|---------|-------------|
| 自定义语音到文本 | 2核，2 GB 内存 | 4核，4 GB 内存 |

# <a name="text-to-speechtabtts"></a>[文本转语音](#tab/tts)

| 容器 | 最小值 | 推荐 |
|-----------|---------|-------------|
| 文本转语音 | 单核，2 GB 内存 | 2核，3 GB 内存 |

# <a name="custom-text-to-speechtabctts"></a>[自定义文本到语音转换](#tab/ctts)

| 容器 | 最小值 | 推荐 |
|-----------|---------|-------------|
| 自定义文本到语音转换 | 单核，2 GB 内存 | 2核，3 GB 内存 |

***

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

> [!NOTE]
> 最小和推荐的基于 Docker 限制，而*不*是主机资源。 例如，"语音到文本" 容器内存映射大型语言模型的部分，并*建议*将整个文件放在内存中，这是额外的 4-6 GB。 另外，由于模型正在分页到内存中，因此第一次运行的容器可能需要更长时间。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

以下容器注册表中提供了语音容器图像。

# <a name="speech-to-texttabstt"></a>[语音转文本](#tab/stt)

| 容器 | 存储库 |
|-----------|------------|
| 语音转文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[自定义语音到文本](#tab/cstt)

| 容器 | 存储库 |
|-----------|------------|
| 自定义语音到文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[文本转语音](#tab/tts)

| 容器 | 存储库 |
|-----------|------------|
| 文本转语音 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[自定义文本到语音转换](#tab/ctts)

| 容器 | 存储库 |
|-----------|------------|
| 自定义文本到语音转换 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>用于语音容器的 Docker 拉取

# <a name="speech-to-texttabstt"></a>[语音转文本](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>用于语音到文本的容器的 Docker 拉取

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> `latest` 标记将提取 `en-US` 区域设置。 有关其他区域设置，请参阅[语音到文本区域设置](#speech-to-text-locales)。

#### <a name="speech-to-text-locales"></a>语音到文本区域设置

除 `latest` 之外的所有标记均采用以下格式，并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

以下标记是以下格式的示例：

```
2.0.0-amd64-en-us-preview
```

对于 "**语音到文本**" 容器的所有受支持的区域设置，请参阅[语音到文本图像标记](../containers/container-image-tags.md#speech-to-text)。

# <a name="custom-speech-to-texttabcstt"></a>[自定义语音到文本](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>自定义语音到文本的容器的 Docker 拉取

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> 自定义语音容器的 `locale` 和 `voice` 由容器的自定义模型引入确定。

# <a name="text-to-speechtabtts"></a>[文本转语音](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>文本到语音容器的 Docker 拉取

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 标记将提取 `en-US` 区域设置并 `jessarus` 语音。 有关其他区域设置，请参阅[文本到语音的区域设置](#text-to-speech-locales)。

#### <a name="text-to-speech-locales"></a>文本到语音的区域设置

除 `latest` 之外的所有标记均采用以下格式，并区分大小写：

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

以下标记是以下格式的示例：

```
1.3.0-amd64-en-us-jessarus-preview
```

对于支持的**文本到语音转换**容器的所有区域设置和相应声音，请参阅[文本到语音图像标记](../containers/container-image-tags.md#text-to-speech)。

> [!IMPORTANT]
> 构造*标准文本到语音转换*HTTP POST 时，[语音合成标记语言（SSML）](speech-synthesis-markup.md)消息需要具有 `name` 属性的 `voice` 元素。 该值是对应的容器区域设置和语音，也称为["短名称"](language-support.md#standard-voices)。 例如，`latest` 标记的语音名称应为 `en-US-JessaRUS`。

# <a name="custom-text-to-speechtabctts"></a>[自定义文本到语音转换](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>自定义文本到语音容器的 Docker 拉取

使用[docker pull](https://docs.docker.com/engine/reference/commandline/pull/)命令从容器预览注册表下载容器映像。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> 自定义语音容器的 `locale` 和 `voice` 由容器的自定义模型引入确定。

***

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](speech-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{Endpoint_URI}` 和 `{API_Key}` 值的详细信息，请参阅[收集必需的参数](#gathering-required-parameters)。 还提供了 `docker run` 命令的其他[示例](speech-container-configuration.md#example-docker-run-commands)。

# <a name="speech-to-texttabstt"></a>[语音转文本](#tab/stt)

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

# <a name="custom-speech-to-texttabcstt"></a>[自定义语音到文本](#tab/cstt)

*自定义语音到文本*的容器依赖于自定义语音模型。 自定义模型必须使用[自定义语音门户](https://speech.microsoft.com/customspeech)进行[训练](how-to-custom-speech-train-model.md)。 运行容器需要自定义语音**模型 ID** 。 可在自定义语音门户的 "**培训**" 页找到此功能。 在自定义语音门户中，导航到**定型**页面并选择模型。
<br>

![自定义语音培训页](media/custom-speech/custom-speech-model-training.png)

获取要用作 `docker run` 命令的 `ModelId` 参数的参数的**模型 ID** 。
<br>

![自定义语音模型详细信息](media/custom-speech/custom-speech-model-details.png)

下表表示各种 `docker run` 参数及其相应的说明：

| 参数 | Description |
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
* 如果 `ModelId`，则会下载模型（如果在卷装载上找不到）。
* 如果以前下载过自定义模型，则将忽略 `ModelId`。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="text-to-speechtabtts"></a>[文本转语音](#tab/tts)

若要运行*文本到语音的*容器，请执行以下 `docker run` 命令。

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

# <a name="custom-text-to-speechtabctts"></a>[自定义文本到语音转换](#tab/ctts)

自定义的*文本到语音转换*容器依赖于自定义语音模型。 自定义模型必须使用[自定义语音门户](https://aka.ms/custom-voice-portal)进行[训练](how-to-custom-voice-create-voice.md)。 需要自定义语音**模型 ID**才能运行容器。 可在自定义语音门户的 "**培训**" 页中找到。 在自定义语音门户中，导航到**定型**页面并选择模型。
<br>

![自定义语音培训页](media/custom-voice/custom-voice-model-training.png)

获取要用作 docker run 命令的 `ModelId` 参数的参数的**模型 ID** 。
<br>

![自定义语音模型详细信息](media/custom-voice/custom-voice-model-details.png)

下表表示各种 `docker run` 参数及其相应的说明：

| 参数 | Description |
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
* 如果 `ModelId`，则会下载模型（如果在卷装载上找不到）。
* 如果以前下载过自定义模型，则将忽略 `ModelId`。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

***

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

| 容器 | SDK 主机 URL | 协议 |
|--|--|--|
| 语音到文本和自定义语音到文本 | `ws://localhost:5000` | WS |
| 文本到语音转换和自定义文本到语音转换 | `http://localhost:5000` | HTTP |

有关使用 WSS 和 HTTPS 协议的详细信息，请参阅[容器安全性](../cognitive-services-container-support.md#azure-cognitive-services-container-security)。

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

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

语音容器使用 Azure 帐户中的*语音*资源将计费信息发送到 azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](speech-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

本文介绍了用于下载、安装和运行语音容器的概念和工作流。 综上所述：

* 语音为 Docker 提供四个 Linux 容器，封装各种功能：
  * *语音转文本*
  * *自定义语音到文本*
  * *文本转语音*
  * *自定义文本到语音转换*
* 容器映像从 Azure 中的容器注册表下载。
* 容器映像在 Docker 中运行。
* 您可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用语音容器中的操作。
* 在实例化容器时，需要提供计费信息。

> [!IMPORTANT]
>  如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看配置设置的[容器](speech-container-configuration.md)
* 了解如何[结合使用 Speech service 容器和 Kubernetes 和 Helm](speech-container-howto-on-premises.md)
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
