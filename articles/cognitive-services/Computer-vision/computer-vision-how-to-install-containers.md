---
title: 如何安装和运行容器 - 计算机视觉
titlesuffix: Azure Cognitive Services
description: 如何在本演练教程中下载、安装和运行计算机视觉容器。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: aba8f9b2b4e62420ed5d318be40bbc4ada544866
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203083"
---
# <a name="install-and-run-recognize-text-containers"></a>安装和运行识别文本容器

计算机视觉的识别文本部分也可用作 Docker 容器。 使用它可以从具有不同表面和背景的各种对象的图像中检测和提取印刷文本，例如收据、海报和名片。  
> [!IMPORTANT]
> 识别文本容器目前仅适用于英语。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

使用“识别文本”容器前，必须先满足以下先决条件：

|需要|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|Azure`Cognitive Services`资源 |若要使用容器，必须具有：<br><br>一个_认知服务_Azure 资源和关联的计费密钥计费终结点 URI。 这两个值的资源概述和密钥页上可用，并且要求来启动该容器。 您需要添加`vision/v2.0`路由到终结点 URI，如下面的 BILLING_ENDPOINT_URI 示例中所示。 <br><br>**{BILLING_KEY}**：资源密钥<br><br>**{BILLING_ENDPOINT_URI}**：终结点 URI 示例如下：`https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表列出了为每个“识别文本”容器分配的最小和建议 CPU 核心数和内存。

| 容器 | 最小值 | 建议 |TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
|识别文本|单核，8GB 内存，0.5 TPS|双核，8GB 内存，1 TPS|0.5, 1|

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。
* TPS - 每秒事务数

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

“识别文本”的容器映像可用。 

| 容器 | 存储库 |
|-----------|------------|
|识别文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

运行 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下载容器映像。


### <a name="docker-pull-for-the-recognize-text-container"></a>适用于“识别文本”容器的 Docker 拉取

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](computer-vision-resource-container-config.md)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 该命令使用以下参数：

| 占位符 | Value |
|-------------|-------|
|{BILLING_KEY} | 此密钥用于启动此容器，并可在 Azure 上`Cognitive Services`密钥页。  |
|{BILLING_ENDPOINT_URI} | 帐单终结点 URI 值。 下面是示例： `https://westus.api.cognitive.microsoft.com/vision/v2.0`|

您需要添加`vision/v2.0`路由到终结点 URI，如下面的 BILLING_ENDPOINT_URI 示例中所示。

在以下示例 `docker run` 命令中，请将这些参数替换为自己的值。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 通过容器映像运行“识别文本”容器
* 分配一个 CPU 核心和 4 GB 内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](./computer-vision-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 `https://localhost:5000`，以获得容器 API。

### <a name="asynchronous-text-recognition"></a>异步文本识别

可以同时使用 `POST /vision/v2.0/recognizeText` 和 `GET /vision/v2.0/textOperations/*{id}*` 操作来异步识别图像中的印刷文本，其方式类似于计算机视觉服务使用相应的 REST 操作。 此时，识别文本容器仅识别印刷文本而无法识别手写文本，因此识别文本容器将忽略通常为计算机视觉服务操作指定的 `mode` 参数。

### <a name="synchronous-text-recognition"></a>同步文本识别

可使用 `POST /vision/v2.0/recognizeTextDirect` 操作以同步方式识别图像中的印刷文本。 由于此操作是同步的，因此此操作的请求正文与 `POST /vision/v2.0/recognizeText` 操作的请求正文相同，但此操作的响应正文与 `GET /vision/v2.0/textOperations/*{id}*` 操作返回的响应正文相同。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](./computer-vision-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。 


## <a name="billing"></a>计费

“识别文本”容器使用 Azure 帐户中的“识别文本”资源向 Azure 发送帐单信息。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./computer-vision-resource-container-config.md)。

## <a name="summary"></a>摘要

本文介绍了与下载、安装和运行“识别文本”容器相关的概念和工作流。 综上所述：

* “识别文本”为 Docker 提供了Linux 容器，用于封装识别文本。
* 从 Azure 中的 Microsoft 容器注册表 (MCR) 下载容器映像。
* 容器映像在 Docker 中运行。
* 可使用 REST API 或 SDK 通过指定容器的主机 URI 来调用“识别文本”容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](computer-vision-resource-container-config.md)了解配置设置
* 查看[计算机视觉概述](Home.md)，了解有关识别印刷文本和手写文本的详细信息  
* 有关该容器支持的方法的详细信息，请参阅[计算机视觉 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)。
* 参阅[常见问题解答 (FAQ)](FAQ.md)，以解决与计算机视觉功能相关的问题。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
