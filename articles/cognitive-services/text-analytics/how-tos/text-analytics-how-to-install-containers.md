---
title: 安装和运行容器
titleSuffix: Text Analytics -  Azure Cognitive Services
description: 通过本演练教程了解如何下载、安装和运行文本分析容器。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 07fbf51f584d691b08d94f68fefa8c3f9348227f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55294822"
---
# <a name="install-and-run-text-analytics-containers"></a>安装和运行文本分析容器

文本分析容器提供对原始文本的高级自然语言处理，并且包含三项主要功能：情绪分析、关键短语提取和语言检测。 容器当前不支持实体链接。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要运行任何文本分析容器，必须具有以下各项：

## <a name="preparation"></a>准备工作

使用文本分析容器之前，必须满足以下先决条件：

|必选|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|文本分析资源 |若要使用容器，必须具有：<br><br>[_文本分析_](text-analytics-how-to-access-key.md) Azure 资源，用于获取关联的计费密钥和计费终结点 URI。 这两个值可以从 Azure 门户中的“文本分析概述”和“密钥”页面获得，并且是启动容器时所必需的。<br><br>**{BILLING_KEY}**：资源密钥<br><br>**{BILLING_ENDPOINT_URI}**：终结点 URI 示例如下：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Request access to private preview](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了每个文本分析容器的 CPU 和内存配置，其中包括要分配的最少和建议 CPU 核心数（至少 2.6 GHz）和内存量 (GB)。

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
|关键短语提取 | 单核，2 GB 内存 | 单核，4 GB 内存 |
|语言检测 | 单核，2 GB 内存 | 单核，4 GB 内存 |
|情绪分析 | 单核，2 GB 内存 | 单核，4 GB 内存 |

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

Microsoft 容器注册表中提供了文本分析的容器映像。 

| 容器 | 存储库 |
|-----------|------------|
|关键短语提取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|语言检测 | `mcr.microsoft.com/azure-cognitive-services/language` |
|情绪分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器可用标记的完整说明，请查看 Docker 中心内的以下容器：

* [关键短语提取](https://go.microsoft.com/fwlink/?linkid=2018757)
* [语言检测](https://go.microsoft.com/fwlink/?linkid=2018759)
* [情绪分析](https://go.microsoft.com/fwlink/?linkid=2018654)

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下载容器映像。


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>适用于关键短语提取容器的 Docker 拉取

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>适用于语言检测容器的 Docker 拉取

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>适用于情绪容器的 Docker 拉取

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](../text-analytics-resource-container-config.md#example-docker-run-commands)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行三个容器中的任意一个。 该命令使用以下参数：

| 占位符 | 值 |
|-------------|-------|
|{BILLING_KEY} | 此密钥用来启动容器，可以从 Azure 门户中的“文本分析密钥”页面上获得。  |
|{BILLING_ENDPOINT_URI} | 可以从 Azure 门户中的“文本分析概述”页面上获得计费终结点 URI 值。|

在以下示例 `docker run` 命令中，请将这些参数替换为自己的值。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 从容器映像运行关键短语容器
* 分配一个 CPU 核心和 4 GB 内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](../text-analytics-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 https://localhost:5000，以获得容器 API。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](../text-analytics-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。 

## <a name="containers-api-documentation"></a>容器的 API 文档

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>计费

文本分析容器使用 Azure 帐户中的_文本分析_资源向 Azure 发送账单信息。 

如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据发送给 Microsoft。 

`docker run` 命令使用以下参数进行计费：

| 选项 | 说明 |
|--------|-------------|
| `ApiKey` | 用于跟踪账单信息的_文本分析_资源的 API 密钥。 |
| `Billing` | 用于跟踪账单信息的_文本分析_资源的终结点。|
| `Eula` | 表示已接受容器的许可条款。<br/>此选项的值必须设置为 `accept`。 |

> [!IMPORTANT]
> 必须使用有效值指定所有三个选项，否则容器将无法启动。

有关这些选项的详细信息，请参阅[配置容器](../text-analytics-resource-container-config.md)。

## <a name="summary"></a>摘要

在本文中，我们已学习相关的概念，以及文本分析容器的下载、安装和运行工作流。 综上所述：

* 文本分析提供三个适用于 Docker 的 Linux 容器，用于封装关键短语提取、语言检测和情绪分析。
* 从 Azure 中的 Microsoft 容器注册表 (MCR) 下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用文本分析容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](../text-analytics-resource-container-config.md)了解配置设置
* 参阅[常见问题解答 (FAQ)](../text-analytics-resource-faq.md) 解决与功能相关的问题。

