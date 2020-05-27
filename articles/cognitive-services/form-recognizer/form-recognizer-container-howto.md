---
title: 如何安装和运行窗体识别器的容器
titleSuffix: Azure Cognitive Services
description: 本文介绍如何使用 Azure 认知服务窗体识别器容器分析窗体和表数据。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 761cf5831bb2332ae8c3f7a9a8edee707e6810ee
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816329"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>安装和运行窗体识别器容器（预览）

Azure 表单识别器应用机器学习技术从表单中识别和提取键值对和表。 它将值和表项与键值对相关联，然后输出包含原始文件中的关系的结构化数据。 

若要降低复杂性并轻松地将自定义表单识别器模型集成到工作流自动化过程或其他应用程序中，可以使用简单的 REST API 来调用该模型。 只需要五个窗体文档（或一个空窗体和两个填充窗体），因此你可以快速、准确地获取结果并针对特定内容进行定制。 而无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。 它不要求标记或标注数据。

> [!IMPORTANT]
> 窗体识别器容器当前使用版本1.0 的窗体识别器 API。 可以改为使用托管服务访问 API 的最新版本。

| 函数 | 功能 |
|----------|----------|
| 表单识别器 | <li>处理 PDF、PNG 和 JPG 文件<li>使用最少五个窗体的相同布局的培训自定义模型 <li>提取键值对和表信息 <li>使用 Azure 认知服务计算机视觉 API 文本识别功能从表单内部的图像中检测和提取印刷体文本<li>无需进行标注或标记 |

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

使用表单识别器容器之前，必须满足以下先决条件：

| 必选 | 目的 |
|----------|---------|
| Docker 引擎 | 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br> |
| 熟悉 Docker | 应对 Docker 概念（例如注册表、存储库、容器和容器映像）有一个基本的理解，并了解基本的 `docker` 命令。 |
| Azure CLI | 在主机上安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 |
| 计算机视觉 API 资源 | 若要处理扫描的文档和图像，需要计算机视觉资源。 可以访问作为 Azure 资源（REST API 或 SDK）或 *cognitive-services-recognize-text* [容器](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)提供的文本识别功能。 使用此功能需要按普通费率付费。 <br><br>同时传入计算机视觉资源（Azure 云或认知服务容器）的 API 密钥和终结点。 使用此 API 密钥和终结点作为 **{COMPUTER_VISION_API_KEY}** 和 **{COMPUTER_VISION_ENDPOINT_URI}**。<br><br> 如果使用 *cognitive-services-recognize-text* 容器，请确保：<br><br>表单识别器容器的计算机视觉密钥是在 *cognitive-services-recognize-text* 容器的计算机视觉 `docker run` 命令中指定的密钥。<br>计费终结点是容器的终结点（例如 `http://localhost:5000`）。 如果在同一台主机上同时使用计算机视觉容器和表单识别器容器，不能使用默认端口 *5000* 将两者同时启动。 |
| 表单识别器资源 | 若要使用这些容器，必须具有：<br><br>用于获取关联的 API 密钥和终结点 URI 的 Azure**窗体识别器**资源。 Azure 门户**窗体识别器**概述和密钥页上都提供了这两个值，这两个值都是启动容器所必需的。<br><br>**{FORM_RECOGNIZER_API_KEY}**： "密钥" 页上有两个可用的资源键之一<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**： "概述" 页中提供的终结点 |

> [!NOTE]
> 计算机视觉资源名称应为单个单词，无连字符 `-` 或其他任何特殊字符。 此限制旨在确保窗体识别器和识别文本容器兼容性。

## <a name="gathering-required-parameters"></a>正在收集所需参数

所有认知服务容器都需要三个主要参数。 最终用户许可协议（EULA）的值必须为 `accept` 。 此外，需要终结点 URL 和 API 密钥。

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>终结点 URI `{COMPUTER_VISION_ENDPOINT_URI}` 和`{FORM_RECOGNIZER_ENDPOINT_URI}`

"**终结点**URI" 值在相应认知服务资源的 "Azure 门户*概述*" 页上可用。 导航到 "*概述*" 页，将鼠标悬停在终结点上， `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 将显示一个图标。 复制并在需要时使用。

![收集终结点 uri 供以后使用](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>密钥 `{COMPUTER_VISION_API_KEY}` 和`{FORM_RECOGNIZER_API_KEY}`

此密钥用于启动容器，并在相应认知服务资源的 Azure 门户 "密钥" 页上可用。 导航到 "*密钥*" 页，然后单击 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 图标。

![获取两个密钥之一供以后使用](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> 这些订阅密钥用于访问认知服务 API。 不要共享你的密钥。 安全地存储它们，例如，使用 Azure Key Vault。 此外，我们建议定期重新生成这些密钥。 发出 API 调用只需一个密钥。 重新生成第一个密钥时，可以使用第二个密钥来持续访问服务。

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

填写并提交[请求窗体](https://aka.ms/cognitivegate)，请求对容器的访问权限。

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了为每个表单识别器容器分配的最小的和建议的 CPU 核心数和内存：

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 表单识别器 | 2 核心，4 GB 内存 | 4 核心，8 GB 内存 |
| 识别文本 | 1核，8 GB 内存 | 2核，8 GB 内存 |

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。
* 核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

> [!Note]
> 最小值和建议值基于 Docker 限制，而不是基于主机资源。**

## <a name="get-the-container-images-with-the-docker-pull-command"></a>通过 docker pull 命令获取容器映像

以下容器注册表中提供了**窗体识别器**和**识别文本**产品/服务的容器映像：

| 容器 | 完全限定的映像名称 |
|-----------|------------|
| 表单识别器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| 识别文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

你将需要这两个容器，请注意，[本文外详细介绍](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)了**识别器文本**容器。

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>适用于表单识别器容器的 Docker 拉取

#### <a name="form-recognizer"></a>表单识别器

若要获取表单识别器容器，请使用以下命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>适用于“识别文本”容器的 Docker 拉取

#### <a name="recognize-text"></a>识别文本

若要获取识别文本容器，请使用以下命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

当容器位于[主计算机](#the-host-computer)上以后，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-by-using-the-docker-run-command)。 提供 `docker run` 命令的多个[示例](form-recognizer-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-by-using-the-docker-run-command"></a>使用 docker run 命令运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何[gathering required parameters](#gathering-required-parameters)获取 `{COMPUTER_VISION_ENDPOINT_URI}` 、 `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` 和值的详细信息，请参阅收集必需的参数 `{FORM_RECOGNIZER_API_KEY}` 。

`docker run` 命令的[示例](form-recognizer-container-configuration.md#example-docker-run-commands)可用。

### <a name="form-recognizer"></a>表单识别器

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

此命令：

* 通过容器映像运行表单识别器容器。
* 分配 2 个 CPU 核心和 8 千兆字节 (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。
* 将 /input 和 /output 卷装载到容器。

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>以独立的 docker run 命令形式运行单独的容器

对于托管在本地的同一主机上的表单识别器和文本识别器组合，可运行下面的两个示例 Docker CLI 命令：

在端口 5000 上运行第一个容器。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

在端口 5001 上运行第二个容器。

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
每个后续容器应位于不同的端口上。 

### <a name="run-separate-containers-with-docker-compose"></a>使用 Docker Compose 运行单独的容器

对于托管在本地的同一主机上的表单识别器和文本识别器组合，请查看下面的示例 Docker Compose YAML 文件。 对于 `formrecognizer` 和 `ocr` 容器，文本识别器 `{COMPUTER_VISION_API_KEY}` 必须相同。 `{COMPUTER_VISION_ENDPOINT_URI}` 只在 `ocr` 容器中使用，因为 `formrecognizer` 容器使用 `ocr` 名称和端口。 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing`、`ApiKey`、`FormRecognizer:ComputerVisionApiKey` 和 `FormRecognizer:ComputerVisionEndpointUri` 选项来运行容器；否则，该容器不会启动。 有关详细信息，请参阅[计费](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

|容器|端点|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>表单识别器

该容器提供基于 WebSocket 的查询终结点 API，可通过[表单识别器服务 SDK 文档](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)访问这些 API。

默认情况下，表单识别器 SDK 使用联机服务。 若要使用该容器，需要更改初始化方法。 请参阅下面的示例。

#### <a name="for-c"></a>对于 C#

请从使用此 Azure 云初始化调用：

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
更改为使用容器终结点的此调用：

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>对于 Python

请从使用此 Azure 云初始化调用：

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

更改为使用容器终结点的此调用：

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>表单识别器

该容器提供 REST 终结点 API，可在 [Form Recognizer API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)（表单识别器 API）页上找到这些 API。


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](form-recognizer-container-configuration.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

表单识别器容器使用 Azure 帐户中的“表单识别器”资源向 Azure 发送账单信息。__

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](form-recognizer-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

本文介绍了与下载、安装和运行表单识别器容器相关的概念与工作流。 综上所述：

* 表单识别器提供一个适用于 Docker 的 Linux 容器。
* 可从 Azure 中的专用容器注册表下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 REST SDK 通过指定容器的主机 URI 来调用表单识别器容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
>  如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](form-recognizer-container-configuration.md)，了解配置设置。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)。
