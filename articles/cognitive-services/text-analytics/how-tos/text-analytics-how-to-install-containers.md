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
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: efe76323b4159af01f1eaf470d9c1833edd0a186
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702130"
---
# <a name="install-and-run-text-analytics-containers"></a>安装和运行文本分析容器

> [!NOTE]
> * 情绪分析 v3 的容器现已正式发布。 关键短语提取和语言检测容器作为无选通公共预览版提供。
> * 实体链接和 NER 当前不可用作容器。

容器使你能够在自己的环境中运行文本分析 Api，这非常适合你的特定安全和数据管理要求。 文本分析容器提供对原始文本的高级自然语言处理，并包括三个主要功能：情绪分析、关键短语提取和语言检测。 

如果没有 Azure 订阅，请在开始之前先创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 免费帐户限制为每月5000个事务，并且只有**免费**和**标准**<a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">定价层 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>对容器有效。 有关事务请求速率的详细信息，请参阅[数据限制](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits)。

## <a name="prerequisites"></a>先决条件

若要运行任何文本分析容器，你必须拥有主机计算机和容器环境。

## <a name="preparation"></a>准备工作

使用文本分析容器之前，必须满足以下先决条件：

|必需|目标|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> **在 Windows 上**，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|文本分析资源 |若要使用容器，必须具有：<br><br>用于获取关联的 API 密钥和终结点 URI 的 Azure[文本分析资源](../../cognitive-services-apis-create-account.md)。 这两个值可以从 Azure 门户中的“文本分析概述”和“密钥”页面获得，并且是启动容器时所必需的。<br><br>**{API_KEY}**： "**密钥**" 页上有两个可用的资源键之一<br><br>**{ENDPOINT_URI}**： "**概述**" 页中提供的终结点|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了文本分析容器的最低和推荐的规格。 至少需要2千兆字节（GB）的内存，并且每个 CPU 核心必须至少为2.6 千兆赫（GHz）或更快。 还列出了 "每节允许的事务数（TPS）"。

|  | 最低主机规格 | 推荐的主机规格 | 最小 TPS | 最大 TPS|
|---|---------|-------------|--|--|
| **语言检测、关键短语提取**   | 1核，2GB 内存 | 1核，4GB 内存 |15 | 30|
| **情绪分析 v3**   | 1核，2GB 内存 | 4核，8GB 内存 |15 | 30|

CPU 内核和内存对应于 `--cpus` 和 `--memory` 设置，这些设置将用作命令的一部分 `docker run` 。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Microsoft 容器注册表中提供了文本分析的容器映像。

# <a name="sentiment-analysis-v3"></a>[情绪分析 v3](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[关键短语提取（预览）](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[语言检测（预览）](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

***

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用[docker run](https://docs.docker.com/engine/reference/commandline/run/)命令运行容器。 容器将继续运行，直到你将其停止。

将下面的占位符替换为你自己的值：

| 占位符 | Value | 格式或示例 |
|-------------|-------|---|
| **{API_KEY}** | 文本分析资源的键。 可以在资源的 "**密钥和终结点**" 页上的 "Azure 门户中找到它。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 用于访问文本分析 API 的终结点。 可以在资源的 "**密钥和终结点**" 页上的 "Azure 门户中找到它。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

> [!IMPORTANT]
> * 以下各节中的 docker 命令使用反斜杠 `\` 作为行继续符。 根据主机操作系统的要求替换或删除字符。 
> * 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。
> * 情绪分析 v3 容器现已正式发布，它在响应中返回[情绪标签](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)。 关键短语提取和语言检测容器使用 API 的 v2，并处于预览阶段。

# <a name="sentiment-analysis-v3"></a>[情绪分析 v3](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[关键短语提取（预览）](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection-preview"></a>[语言检测（预览）](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。

使用主机 `http://localhost:5000`，以获得容器 API。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑难解答

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

* 文本分析为 Docker 提供三个 Linux 容器，封装各种功能：
   * *情绪分析*
   * *关键短语提取（预览）* 
   * *语言检测（预览）*
   
* 从 Azure 中的 Microsoft 容器注册表 (MCR) 下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用文本分析容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如正在分析的文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看配置设置的[容器](../text-analytics-resource-container-config.md)
* 参阅[常见问题解答 (FAQ)](../text-analytics-resource-faq.md) 解决与功能相关的问题。
