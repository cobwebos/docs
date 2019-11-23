---
title: How to install and run container for Form Recognizer
titleSuffix: Azure Cognitive Services
description: This article will explain how to use the Azure Cognitive Services Form Recognizer container to parse form and table data.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 21582a5a17a3c6f67182173bfe08d80c48765f7d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325840"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Install and run Form Recognizer containers (Preview)

Azure 表单识别器应用机器学习技术从表单中识别和提取键值对和表。 它将值和表项与键值对相关联，然后输出包含原始文件中的关系的结构化数据。 

若要降低复杂性并轻松地将自定义表单识别器模型集成到工作流自动化过程或其他应用程序中，可以使用简单的 REST API 来调用该模型。 Only five form documents (or one empty form and two filled-in forms) are needed, so you can get results quickly, accurately, and tailored to your specific content. 而无需进行繁琐的手动干预，也不需要具备丰富的数据科学专业知识。 它不要求标记或标注数据。

|函数|功能|
|-|-|
|表单识别器| <li>处理 PDF、PNG 和 JPG 文件<li>Trains custom models with a minimum of five forms of the same layout <li>提取键值对和表信息 <li>使用 Azure 认知服务计算机视觉 API 文本识别功能从表单内部的图像中检测和提取印刷体文本<li>无需进行标注或标记|

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

使用表单识别器容器之前，必须满足以下先决条件：

|需要|用途|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念（例如注册表、存储库、容器和容器映像）有一个基本的理解，并了解基本的 `docker` 命令。|
|Azure CLI| 在主机上安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。|
|计算机视觉 API 资源| 若要处理扫描的文档和图像，需要计算机视觉资源。 可以访问作为 Azure 资源（REST API 或 SDK）或 *cognitive-services-recognize-text* [容器](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)提供的文本识别功能。 使用此功能需要按普通费率付费。 <br><br>Pass in both the API key and endpoints for your Computer Vision resource (Azure cloud or Cognitive Services container). Use this API key and the endpoint as **{COMPUTER_VISION_API_KEY}** and **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> 如果使用 *cognitive-services-recognize-text* 容器，请确保：<br><br>表单识别器容器的计算机视觉密钥是在 *cognitive-services-recognize-text* 容器的计算机视觉 `docker run` 命令中指定的密钥。<br>计费终结点是容器的终结点（例如 `http://localhost:5000`）。 如果在同一台主机上同时使用计算机视觉容器和表单识别器容器，不能使用默认端口 *5000* 将两者同时启动。 |
|表单识别器资源 |若要使用这些容器，必须具有：<br><br>An Azure **Form Recognizer** resource to get the associated API key and endpoint URI. Both values are available on the Azure portal **Form Recognizer** Overview and Keys pages, and both values are required to start the container.<br><br>**{FORM_RECOGNIZER_API_KEY}** : One of the two available resource keys on the Keys page<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : The endpoint as provided on the Overview page|

## <a name="gathering-required-parameters"></a>Gathering required parameters

There are three primary parameters for all Cognitive Services' containers that are required. The end-user license agreement (EULA) must be present with a value of `accept`. Additionally, both an Endpoint URL and API Key are needed.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Endpoint URI `{COMPUTER_VISION_ENDPOINT_URI}` and `{FORM_RECOGNIZER_ENDPOINT_URI}`

The **Endpoint** URI value is available on the Azure portal *Overview* page of the corresponding Cognitive Service resource. Navigate to the *Overview* page, hover over the Endpoint, and a `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icon will appear. Copy and use where needed.

![Gather the endpoint uri for later use](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Keys `{COMPUTER_VISION_API_KEY}` and `{FORM_RECOGNIZER_API_KEY}`

This key is used to start the container, and is available on the Azure portal's Keys page of the corresponding Cognitive Service resource. Navigate to the *Keys* page, and click on the `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icon.

![Get one of the two keys for later use](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> These subscription keys are used to access your Cognitive Service API. Do not share your keys. Store them securely, for example, using Azure Key Vault. We also recommend regenerating these keys regularly. Only one key is necessary to make an API call. When regenerating the first key, you can use the second key for continued access to the service.

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

在请求访问该容器之前，必须先填写并提交[认知服务表单识别器容器访问请求表单](https://aka.ms/FormRecognizerRequestAccess)。 这也会注册计算机视觉。 不需要单独注册计算机视觉请求表单。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了为每个表单识别器容器分配的最小的和建议的 CPU 核心数和内存：

| 容器 | 最小值 | 推荐 |
|-----------|---------|-------------|
| 表单识别器 | 2 core, 4-GB memory | 4 core, 8-GB memory |
| 识别文本 | 1 core, 8-GB memory | 2 cores, 8-GB memory |

* 每个核心必须至少为 2.6 千兆赫（GHz）或更快。
* 核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

> [!Note]
> 最小值和建议值基于 Docker 限制，而不是基于主机资源。

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Get the container images with the docker pull command

Container images for both the **Form Recognizer** and **Recognize Text** offerings are available in the following container registry:

| 容器 | Fully qualified image name |
|-----------|------------|
| 表单识别器 | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| 识别文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

You will need both containers, please note that the **Recognizer Text** container is [detailed outside of this article.](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>适用于表单识别器容器的 Docker 拉取

#### <a name="form-recognizer"></a>表单识别器

若要获取表单识别器容器，请使用以下命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>适用于“识别文本”容器的 Docker 拉取

#### <a name="recognize-text"></a>识别文本

To get the Recognize Text container, use the following command:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

当容器位于[主计算机](#the-host-computer)上以后，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-by-using-the-docker-run-command)。 提供 `docker run` 命令的多个[示例](form-recognizer-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-by-using-the-docker-run-command"></a>使用 docker run 命令运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{COMPUTER_VISION_ENDPOINT_URI}`, `{COMPUTER_VISION_API_KEY}`, `{FORM_RECOGNIZER_ENDPOINT_URI}` and `{FORM_RECOGNIZER_API_KEY}` values.

[Examples](form-recognizer-container-configuration.md#example-docker-run-commands) of the `docker run` command are available.

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

|容器|终结点|
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

表单识别器容器使用 Azure 帐户中的“表单识别器”资源向 Azure 发送账单信息。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](form-recognizer-container-configuration.md)。

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>总结

本文介绍了与下载、安装和运行表单识别器容器相关的概念与工作流。 摘要：

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
