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
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7708133fcba0d594ecd420afd8da1b2881055aa7
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241016"
---
# <a name="install-and-run-speech-service-containers"></a>安装和运行语音服务容器

语音容器使客户能够构建一种经过优化的语音应用程序体系结构，以利用强大的云功能和边缘区域。 

这两个语音容器是**语音到文本**和**文本到语音**转换。 

|函数|功能|最新|
|-|-|--|
|语音转文本| <li>转录连续实时语音或批处理音频记录到带有中间结果的文本中。|1.2.0|
|文本到语音转换| <li>将文本转换为自然发音的语音。 带有纯文本输入或语音合成标记语言（SSML）。 |1.2.0|

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

使用语音容器之前，必须满足以下先决条件：

|必填|用途|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|语音资源 |若要使用这些容器，必须具有：<br><br>用于获取关联的 API 密钥和终结点 URI 的 Azure_语音_资源。 Azure 门户的 "**语音**概述" 和 "密钥" 页上都有这两个值。 它们都是启动容器所必需的。<br><br>**{API_KEY}** ："**密钥**" 页上有两个可用的资源键之一<br><br>**{ENDPOINT_URI}** ："**概述**" 页中提供的终结点|

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

必须先完成并提交[认知服务语音容器请求表单](https://aka.ms/speechcontainerspreview/)，才能请求访问容器。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

主机是运行 docker 容器的计算机。 主机必须支持[高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2)（AVX2）。 可以通过以下命令在 Linux 主机上查看此支持： 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表介绍了为每个语音容器分配的最低和推荐的 CPU 内核和内存。

| 容器 | 最低要求 | 建议 |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2核心<br>2 GB 内存  | 4核<br>4 GB 内存  |
|cognitive-services-text-to-speech | 1核，0.5 GB 内存| 2核，1 GB 内存 |

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

**注意**;最小和推荐的基于 Docker 限制，而*不*是主机资源。 例如，"语音到文本" 容器内存映射大型语言模型的部分，并_建议_将整个文件放在内存中，这是额外的 4-6 GB。 另外，由于模型正在分页到内存中，因此第一次运行的容器可能需要更长时间。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

可用的语音容器图像。

| 容器 | 存储库 |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>语言区域设置在容器标记中

标记将提取区域设置和`jessarus`语音。 `en-us` `latest`

#### <a name="speech-to-text-locales"></a>语音到文本区域设置

除之外`latest`的所有标记均采用以下格式， `<culture>`其中指示区域设置容器：

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

以下标记是以下格式的示例：

```
1.2.0-amd64-en-us-preview
```

下表列出了1.2.0 版本的容器中的**语音到文本**的支持区域设置：

|语言区域设置|Tags|
|--|--|
|中文|`zh-cn`|
|英语 |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|法语 |`fr-ca`<br>`fr-fr`|
|德语|`de-de`|
|意大利语|`it-it`|
|日语|`ja-jp`|
|韩语|`ko-kr`|
|葡萄牙语|`pt-br`|
|西班牙语|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>文本到语音区域设置

除之外`latest`的所有标记均采用以下格式， `<culture>`其中`<voice>`指示区域设置，指示容器的语音：

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

以下标记是以下格式的示例：

```
1.2.0-amd64-en-us-jessarus-preview
```

下表列出了1.2.0 版本的容器中的**文本到语音功能**的支持区域设置：

|语言区域设置|Tags|支持的语音|
|--|--|--|
|中文|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|英语 |`en-au`|catherine<br>hayleyrus|
|英语 |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|英语 |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|英语 |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|法语|`fr-ca`|caroline<br>harmonierus|
|法语|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|德语|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|意大利语|`it-it`|cosimo-apollo<br>luciarus|
|日语|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|韩语|`ko-kr`|heamirus|
|葡萄牙语|`pt-br`|daniel-apollo<br>heloisarus|
|西班牙语|`es-es`|elenarus<br>刘娜-apollo<br>pablo-apollo<br>|
|西班牙语|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>用于语音容器的 Docker 拉取

#### <a name="speech-to-text"></a>语音转文本

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>文本转语音

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](speech-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行三个容器中的任意一个。 该命令使用以下参数：

在**预览期间**，计费设置必须有效才能启动容器，但不会对使用情况收费。

| 占位符 | ReplTest1 |
|-------------|-------|
|{API_KEY} | 此密钥用于启动容器，并在 Azure 门户的语音密钥页上可用。  |
|{ENDPOINT_URI} | Azure 门户的语音概述页上提供了 "计费终结点 URI" 值。|

在以下示例 `docker run` 命令中，请将这些参数替换为自己的值。

### <a name="text-to-speech"></a>文本转语音

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>语音转文本

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行语音容器
* 分配2个 CPU 内核和2千兆字节（GB）的内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

|容器|终结点|
|--|--|
|语音转文本|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|文本转语音|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>语音转文本

容器提供基于 websocket 的查询终结点 Api，可通过[语音 SDK](index.yml)进行访问。

默认情况下，语音 SDK 使用联机语音服务。 若要使用该容器，需要更改初始化方法。 请参阅下面的示例。

#### <a name="for-c"></a>对于 C#

请从使用此 Azure 云初始化调用：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

更改为使用容器终结点发出此调用：

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>对于 Python

请从使用此 Azure 云初始化调用

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

更改为使用容器终结点发出此调用：

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>文本转语音

容器提供了 REST 终结点 Api，可以在[此处](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)找到，[此处](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)提供了示例。

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑难解答

运行该容器时，该容器将使用 **stdout** 和 **stderr** 来输出信息，这些信息有助于排查启动或运行容器时发生的问题。

## <a name="billing"></a>帐单

语音容器使用 Azure 帐户中的_语音_资源将计费信息发送到 azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](speech-container-configuration.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>总结

本文介绍了用于下载、安装和运行语音容器的概念和工作流。 综上所述：

* 语音为 Docker 提供两个 Linux 容器，将语音封装到文本和文本到语音。
* 可从 Azure 中的专用容器注册表下载容器映像。
* 容器映像在 Docker 中运行。
* 您可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用语音容器中的操作。
* 在实例化容器时，需要提供计费信息。

> [!IMPORTANT]
>  如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](speech-container-configuration.md)了解配置设置
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
