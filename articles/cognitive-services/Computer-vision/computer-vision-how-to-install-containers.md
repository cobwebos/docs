---
title: 如何安装和运行容器 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 如何在本演练教程中下载、安装和运行计算机视觉容器。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 310a7578fe9d3af8c4600968178cb677658feaa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254323"
---
# <a name="install-and-run-read-containers-preview"></a>安装和运行读取容器（预览版）

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

借助容器，你可以在自己的环境中运行计算机视觉 API。 容器非常适合用于满足特定的安全性和数据管理要求。 在本文中，你将学习如何下载、安装和运行计算机视觉容器。

借助读取** 容器，可以从具有不同表面和背景的各种对象的图像中检测和提取印刷文本**，例如收据、海报和名片。 此外，读取** 容器可检测图像中的手写** 文本，并提供 PDF、TIFF 和多页文件支持。 有关详细信息，请参阅[读取](concept-recognizing-text.md#read-api) API 文档。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

使用容器前，必须满足以下先决条件：

|必须|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> **** 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|计算机视觉资源 |若要使用容器，必须具有：<br><br>Azure 计算机视觉**** 资源和关联的 API 密钥及终结点 URI。 这两个值都可以在资源的“概述”和“密钥”页上找到，并且是启动容器所必需的。<br><br>**{API_KEY}** ：“密钥”页上提供的两个可用资源密钥中的一个****<br><br>**{ENDPOINT_URI}** ：“概述”页上提供的终结点****|

## <a name="request-approval-to-run-the-container"></a>请求批准以运行容器

填写并提交 [请求表单](https://aka.ms/csgate) ，请求批准以运行容器。 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>高级矢量扩展支持

主**** 计算机是运行 docker 容器的计算机。 主机 *必须支持* (AVX2) 的 [高级矢量扩展](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) 。 可使用以下命令检查 Linux 主机是否提供 AVX2 支持：

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> 需要** 主计算机来支持 AVX2。 如果没有 AVX2 支持，容器将无法** 正常运行。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

提供适用于读取的容器映像。

| 容器 | 容器注册表/存储库/映像名称 |
|-----------|------------|
| 阅读 3.0-预览 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| 阅读 3.1-预览 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下载容器映像。

### <a name="docker-pull-for-the-read-container"></a>适用于读取容器的 Docker 拉取

# <a name="version-31-preview"></a>[版本 3.1-预览版](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](computer-vision-resource-container-config.md)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{ENDPOINT_URI}` 和 `{API_KEY}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。

`docker run` 命令的[示例](computer-vision-resource-container-config.md#example-docker-run-commands)可用。

# <a name="version-31-preview"></a>[版本 3.1-预览版](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行读取容器。
* 分配8个 CPU 核心和 18 gb (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

# <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

此命令：

* 从容器映像运行读取容器。
* 分配8个 CPU 核心和 18 gb (GB) 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

---


提供 `docker run` 命令的多个[示例](./computer-vision-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

如果需要更高的吞吐量 (例如，) 处理多页文件时，请考虑使用[Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-account-create)和[Azure 队列](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction)[在 Kubernetes 群集上](deploy-computer-vision-on-premises.md)部署多个3.0 或3.1 个容器。

如果使用 Azure 存储来存储图像以进行处理，则可以在调用容器时创建要使用的 [连接字符串](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) 。

查找连接字符串：

1. 导航到 Azure 门户上的 " **存储帐户** "，然后找到你的帐户。
2. 单击左侧导航列表中的 " **访问密钥** "。
3. 连接字符串将位于**连接字符串**下面

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

# <a name="version-31-preview"></a>[版本 3.1-预览版](#tab/version-3-1)

为容器 API 使用主机 `http://localhost:5000`。 可以在以下位置查看 Swagger 路径： `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` 。

# <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

为容器 API 使用主机 `http://localhost:5000`。 可以在以下位置查看 Swagger 路径： `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` 。

---

### <a name="asynchronous-read"></a>异步读取


# <a name="version-31-preview"></a>[版本 3.1-预览版](#tab/version-3-1)

可以同时使用 `POST /vision/v3.1/read/analyze` 和 `GET /vision/v3.1/read/operations/{operationId}` 操作来异步读取图像，类似于计算机视觉服务使用相应 REST 操作的方式。 异步 POST 方法将返回一个 `operationId`，它用作 HTTP GET 请求的标识符。


在 Swagger UI 中，选择 `asyncBatchAnalyze` 以在浏览器中将其展开。 然后选择“试用”**** > “选择文件”****。 在本示例中，我们将使用以下图像：

![制表符与空格](media/tabs-vs-spaces.png)

异步 POST 成功运行后，它会返回 HTTP 202**** 状态代码。 作为响应的一部分，有一个 `operation-location` 标头，其中包含请求的结果终结点。

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` 是完全限定的 URL，可通过 HTTP GET 访问。 以下是从上图执行 `operation-location` URL 的 JSON 响应：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

可以同时使用 `POST /vision/v3.0/read/analyze` 和 `GET /vision/v3.0/read/operations/{operationId}` 操作来异步读取图像，类似于计算机视觉服务使用相应 REST 操作的方式。 异步 POST 方法将返回一个 `operationId`，它用作 HTTP GET 请求的标识符。

在 Swagger UI 中，选择 `asyncBatchAnalyze` 以在浏览器中将其展开。 然后选择“试用”**** > “选择文件”****。 在本示例中，我们将使用以下图像：

![制表符与空格](media/tabs-vs-spaces.png)

异步 POST 成功运行后，它会返回 HTTP 202**** 状态代码。 作为响应的一部分，有一个 `operation-location` 标头，其中包含请求的结果终结点。

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` 是完全限定的 URL，可通过 HTTP GET 访问。 以下是从上图执行 `operation-location` URL 的 JSON 响应：

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

> [!IMPORTANT]
> 如果将多个读取容器部署在负载平衡器之后（例如，在 "Docker Compose" 或 "Kubernetes" 下），则必须具有外部缓存。 由于处理容器和 GET 请求容器可能不相同，因此外部缓存会存储结果并在容器之间共享这些结果。 有关缓存设置的详细信息，请参阅 [Configure 计算机视觉 Docker 容器](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config)。

### <a name="synchronous-read"></a>同步读取

您可以使用以下操作来同步读取图像。 

# <a name="version-31-preview"></a>[版本 3.1-预览版](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[版本 3.0-preview](#tab/version-3)

`POST /vision/v3.0/read/syncAnalyze`

---

完整读取图像后，API 才会返回 JSON 响应。 唯一的例外是发生错误的情况。 发生错误时，将返回以下 JSON：

```json
{
    "status": "Failed"
}
```

JSON 响应对象具有与异步版本相同的对象图。 如果你是 JavaScript 用户并且需要类型安全，请考虑使用 TypeScript 来强制转换 JSON 响应。

有关示例用例，请<a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">在此处查看 TypeScript 沙盒<span class="docon docon-navigate-external x-hidden-focus"></span></a>，然后选择“运行”**** 以直观显示其易用性。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](./computer-vision-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

认知服务容器使用 Azure 帐户中的相应资源向 Azure 发送计费信息。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./computer-vision-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，你已学习相关概念，以及计算机视觉容器的下载、安装和运行工作流。 综上所述：

* 计算机视觉为 Docker 提供 Linux 容器，用于封装读取。
* 可从 Azure 中的“容器预览版”容器注册表下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用读取容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](computer-vision-resource-container-config.md)了解配置设置
* 查看[计算机视觉概述](overview.md)，了解有关识别印刷文本和手写文本的详细信息
* 有关该容器支持的方法的详细信息，请参阅[计算机视觉 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)。
* 参阅[常见问题解答 (FAQ)](FAQ.md)，以解决与计算机视觉功能相关的问题。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
