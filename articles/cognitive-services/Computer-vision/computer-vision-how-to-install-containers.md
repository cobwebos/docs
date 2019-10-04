---
title: 如何安装和运行容器 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 如何在本演练教程中下载、安装和运行计算机视觉容器。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 7c137572fadd07254343b7b4c34b5a63534b9d88
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937000"
---
# <a name="install-and-run-computer-vision-containers"></a>安装并运行计算机视觉容器

容器使你能够在自己的环境中运行计算机视觉 Api。 容器非常适合于特定的安全和数据管理要求。 本文介绍如何下载、安装和运行计算机视觉容器。

有两个 Docker 容器可用于计算机视觉：*识别文本*和*读取*。 利用*识别文本*容器，你可以通过不同的图面和背景（如收据、海报和名片）来检测和提取不同对象图像中的*打印文本*。 但*读取*容器;还检测图像中的*手写文本*，并提供 PDF/TIFF/多页面支持。 有关详细信息，请参阅[读取 API](concept-recognizing-text.md#read-api)文档。

> [!IMPORTANT]
> 在读取容器时，识别文本容器将被弃用。 Read 容器是识别文本容器中其前置任务的超集，使用者应该迁移到使用读取容器。 这两个容器仅适用于英语。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

使用容器之前，必须满足以下先决条件：

|必填|用途|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|计算机视觉资源 |若要使用容器，必须具有：<br><br>Azure**计算机视觉**资源和关联的 API 密钥。 这两个值都可以在资源的“概述”和“密钥”页上找到，并且是启动容器所必需的。<br><br>**{API_KEY}** ：“密钥”页上提供的两个可用资源密钥中的一个<br><br>**{ENDPOINT_URI}** ：“概述”页上提供的终结点|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

# <a name="readtabread"></a>[读取](#tab/read)

要读取的容器映像可用。

| 容器 | 容器注册表/存储库/映像名称 |
|-----------|------------|
| 读取 | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

# <a name="recognize-texttabrecognize-text"></a>[识别文本](#tab/recognize-text)

“识别文本”的容器映像可用。

| 容器 | 容器注册表/存储库/映像名称 |
|-----------|------------|
| 识别文本 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

***

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下载容器映像。

# <a name="readtabread"></a>[读取](#tab/read)

### <a name="docker-pull-for-the-read-container"></a>读取容器的 Docker 请求

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

# <a name="recognize-texttabrecognize-text"></a>[识别文本](#tab/recognize-text)

### <a name="docker-pull-for-the-recognize-text-container"></a>适用于“识别文本”容器的 Docker 拉取

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](computer-vision-resource-container-config.md)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取`{ENDPOINT_URI}`和`{API_KEY}`值的详细信息，请参阅[收集必需的参数](#gathering-required-parameters)。

可用的[命令示例](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` 。

# <a name="readtabread"></a>[读取](#tab/read)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像中运行读取容器。
* 分配8个 CPU 内核和16千兆字节（GB）的内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="recognize-texttabrecognize-text"></a>[识别文本](#tab/recognize-text)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行识别文本容器。
* 分配8个 CPU 内核和16千兆字节（GB）的内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

***

提供 `docker run` 命令的多个[示例](./computer-vision-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 `http://localhost:5000`，以获得容器 API。

# <a name="readtabread"></a>[读取](#tab/read)

### <a name="asynchronous-read"></a>异步读取

你可以使用`POST /vision/v2.0/read/core/asyncBatchAnalyze`和`GET /vision/v2.0/read/operations/{operationId}`操作来以异步方式读取图像，这与计算机视觉服务使用这些对应 REST 操作的方式类似。 异步 POST 方法将返回一个`operationId` ，用作 HTTP GET 请求的标识符。

在 swagger UI 中，选择`asyncBatchAnalyze`将其在浏览器中展开。 然后选择 "**试用** >  **"。** 在此示例中，我们将使用下图：

![选项卡和空格](media/tabs-vs-spaces.png)

异步 POST 成功运行后，它将返回**HTTP 202**状态代码。 作为响应的一部分，有一个`operation-location`标头，其中包含请求的结果终结点。

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`是完全限定的 URL，通过 HTTP GET 访问。 下面是从上一个映像执行`operation-location` URL 的 JSON 响应：

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [
            56,
            39,
            317,
            50,
            313,
            134,
            53,
            123
          ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [
                90,
                43,
                243,
                53,
                243,
                123,
                94,
                125
              ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [
                259,
                55,
                313,
                62,
                313,
                122,
                259,
                123
              ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [
            221,
            148,
            417,
            146,
            417,
            206,
            227,
            218
          ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [
                230,
                148,
                416,
                141,
                419,
                211,
                232,
                218
              ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>同步读取

您可以使用`POST /vision/v2.0/read/core/Analyze`操作来同步读取图像。 如果图像是完整的，则 API 会返回 JSON 响应。 唯一的例外情况是发生错误。 出现错误时，将返回以下 JSON：

```json
{
    status: "Failed"
}
```

JSON 响应对象与异步版本具有相同的对象关系图。 如果你是 JavaScript 用户并且需要类型安全，则可以使用以下类型将 JSON 响应转换为`AnalyzeResult`对象。

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: string | null;
}
```

有关示例用例，请参阅此处的[TypeScript 沙盒](https://aka.ms/ts-read-api-types)，并选择 "运行" 以可视化其易用性。

# <a name="recognize-texttabrecognize-text"></a>[识别文本](#tab/recognize-text)

### <a name="asynchronous-text-recognition"></a>异步文本识别

可以同时使用 `POST /vision/v2.0/recognizeText` 和 `GET /vision/v2.0/textOperations/*{id}*` 操作来异步识别图像中的印刷文本，其方式类似于计算机视觉服务使用相应的 REST 操作。 此时，识别文本容器仅识别印刷文本而无法识别手写文本，因此识别文本容器将忽略通常为计算机视觉服务操作指定的 `mode` 参数。

### <a name="synchronous-text-recognition"></a>同步文本识别

可使用 `POST /vision/v2.0/recognizeTextDirect` 操作以同步方式识别图像中的印刷文本。 由于此操作是同步的, 因此此操作的请求正文与`POST /vision/v2.0/recognizeText`操作相同, 但此操作的响应正文与`GET /vision/v2.0/textOperations/*{id}*`操作返回的正文相同。

***

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑难解答

如果运行启用了输出[装入点](./computer-vision-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>账单

认知服务容器使用 Azure 帐户中的相应资源将计费信息发送到 Azure。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./computer-vision-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>总结

在本文中，你已学习相关概念，以及计算机视觉容器的下载、安装和运行工作流。 综上所述：

* 计算机视觉为 Docker 提供 Linux 容器，同时封装识别文本和读取。
* 容器映像从 Azure 中的 "容器预览" 容器注册表下载。
* 容器映像在 Docker 中运行。
* 您可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用识别文本或读取容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](computer-vision-resource-container-config.md)了解配置设置
* 查看[计算机视觉概述](Home.md)，了解有关识别印刷文本和手写文本的详细信息
* 有关该容器支持的方法的详细信息，请参阅[计算机视觉 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)。
* 参阅[常见问题解答 (FAQ)](FAQ.md)，以解决与计算机视觉功能相关的问题。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
