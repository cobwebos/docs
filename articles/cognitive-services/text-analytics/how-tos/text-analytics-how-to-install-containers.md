---
title: 安装和运行容器 - 文本分析
titleSuffix: Azure Cognitive Services
description: 通过本演练教程了解如何下载、安装和运行文本分析容器。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a83fd67a9ad5e5a36555fcbaed65b1d76936c33d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906061"
---
# <a name="install-and-run-text-analytics-containers"></a>安装和运行文本分析容器

> [!NOTE]
> * 情绪分析 v3 的容器现已公开发布。 关键短语提取和语言检测容器以非封闭公开预览版的形式发布。
> * 实体链接和 NER 当前不可用作容器。
> * 访问运行状况容器的文本分析需要 [请求窗体](https://aka.ms/cognitivegate)。 目前，不会向你收费。
> * 容器映像位置最近可能已更改。 阅读本文，查看此容器的更新位置。

容器使你能够在自己的环境中运行文本分析 API，最适合特定安全性和数据管理要求。 文本分析容器提供对原始文本的高级自然语言处理，并且包含三项主要功能：情绪分析、关键短语提取和语言检测。 

如果你还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

> [!IMPORTANT]
> 免费帐户限制为每月5000个事务，并且只有**免费**和**标准**<a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">定价层 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>对容器有效。 有关事务请求费率的更多信息，请参阅[数据限制](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits)。

## <a name="prerequisites"></a>先决条件

若要运行任何文本分析容器，必须具有主计算机和容器环境。

## <a name="preparation"></a>准备

使用文本分析容器之前，必须满足以下先决条件：

|必须|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> **** 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|文本分析资源 |若要使用容器，必须具有：<br><br>一个用于获取关联 API 密钥和终结点 URI 的 Azure [文本分析资源](../../cognitive-services-apis-create-account.md)。 这两个值可以从 Azure 门户中的“文本分析概述”和“密钥”页面获得，并且是启动容器时所必需的。<br><br>**{API_KEY}** ：“密钥”页上提供的两个可用资源密钥中的一个****<br><br>**{ENDPOINT_URI}** ：“概述”页上提供的终结点****|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表显示了文本分析容器的最小和建议的资源规范。 至少需要 2 千兆字节 (GB) 的内存，并且每个 CPU 核心必须至少为 2.6 千兆赫 (GHz) 或更快。 还列出了可允许的每节事务数 (TPS)。

|  | 最小主机规格 | 推荐主机规格 | 最小 TPS | 最大 TPS|
|---|---------|-------------|--|--|
| **语言检测、关键短语提取**   | 1 核，2GB 内存 | 1 核，4GB 内存 |15 | 30|
| **情绪分析 v3**   | 1 核，2GB 内存 | 4 核，8GB 内存 |15 | 30|
| **运行状况文本分析 - 1 文档/请求**   |  4 核，10GB 内存 | 6 核，12GB 内存 |15 | 30|
| **运行状况文本分析 - 10 文档/请求**   |  6 核，16GB 内存 | 8 核，20GB 内存 |15 | 30|

CPU 核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Microsoft 容器注册表中提供了文本分析的容器映像。

# <a name="sentiment-analysis-v3"></a>[情绪分析 v3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[关键短语提取（预览版）](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[语言检测（预览版）](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[运行状况文本分析（预览版）](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令来运行容器。 容器将继续运行，直到停止它。

> [!IMPORTANT]
> * 以下各节中的 docker 命令使用反斜杠 `\` 作为行继续符。 根据主机操作系统的要求替换或删除字符。 
> * 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。
> * 情绪分析 v3 容器现已正式发布，它将在响应时返回[情绪标签](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)。 关键字提取和语言检测容器使用 API 的 v2，并且提供预览版。

# <a name="sentiment-analysis-v3"></a>[情绪分析 v3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[关键短语提取（预览版）](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[语言检测（预览版）](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[运行状况文本分析（预览版）](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。

为容器 API 使用主机 `http://localhost:5000`。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](../text-analytics-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

文本分析容器使用 Azure 帐户中的_文本分析_资源向 Azure 发送账单信息。 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](../text-analytics-resource-container-config.md)。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，我们已学习相关的概念，以及文本分析容器的下载、安装和运行工作流。 综上所述：

* 文本分析为 Docker 提供了三个 Linux 容器，其中封装了各种功能：
   * *情绪分析*
   * *关键短语提取（预览版）* 
   * *语言检测（预览版）*
   * *运行状况文本分析（预览版）*
* 容器映像可从 Microsoft 容器注册表 (MCR) 或预览版容器注册表中下载。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用文本分析容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会向 Microsoft 发送客户数据（例如正在分析的文本）。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](../text-analytics-resource-container-config.md)了解配置设置
* 参阅[常见问题解答 (FAQ)](../text-analytics-resource-faq.md) 解决与功能相关的问题。
