---
title: 安装并运行容器-面部
titleSuffix: Azure Cognitive Services
description: 本文介绍如何下载、安装和运行本演练教程中的容器。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 611ab503dfea44e8287e95cf607ce6af3b447d1f
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815785"
---
# <a name="install-and-run-face-containers-preview"></a>安装并运行人脸容器（预览）

Azure 认知服务面部为 Docker 提供标准化的 Linux 容器，用于检测图像中的人脸。 它还标识属性，其中包括 noses 和眼睛、性别、年龄和其他计算机预测的面部功能等人脸特征点。 除了检测以外，人还可以使用置信度分数来检查同一图像或不同图像中的两人脸是否相同。 人脸还可以对照数据库比较人脸，以查看看起来是否有相似或相同的人脸。 它还可以使用共享的视觉特征将相似的人为组。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

必须满足以下先决条件，然后才能使用人脸服务容器。

|必选|目的|
|--|--|
|Docker 引擎| Docker 引擎必须安装在[主计算机](#the-host-computer)上。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 你需要基本了解 Docker 概念，如注册表、存储库、容器和容器映像。 还需要了解基本 `docker` 命令。| 
|人脸资源 |若要使用该容器，必须具备：<br><br>Azure**面部**资源以及关联的 API 密钥和终结点 URI。 此资源的 "**概述**" 和 "**键**" 页上提供了这两个值。 它们是启动容器所必需的。<br><br>**{API_KEY}**： "**密钥**" 页上有两个可用的资源键之一<br><br>**{ENDPOINT_URI}**： "**概述**" 页中提供的终结点

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

填写并提交[请求窗体](https://aka.ms/cognitivegate)，请求对容器的访问权限。 

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表介绍了为每个面部服务容器分配的最低和推荐的 CPU 内核数和内存。

| 容器 | 最小值 | 建议 | 每秒事务数<br>（最小值、最大值）|
|-----------|---------|-------------|--|
|人脸 | 单核，2-GB 内存 | 单核，4-GB 内存 |10, 20|

* 每个核心都必须至少为 2.6 GHz 或更快。
* 每秒事务数（TPS）。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>获取容器映像与 docker pull

面部服务的容器映像可用。 

| 容器 | 存储库 |
|-----------|------------|
| 人脸 | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>适用于人脸容器的 Docker 拉取

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>使用容器

当容器位于[主计算机](#the-host-computer)上以后，请通过以下过程使用容器。

1. [运行](#run-the-container-with-docker-run)具有所需计费设置的容器。 提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>运行包含 docker 的容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 有关如何获取 `{ENDPOINT_URI}` 和 `{API_KEY}` 值的详细信息，请参阅[收集所需的参数](#gathering-required-parameters)。

`docker run` 命令的[示例](face-resource-container-config.md#example-docker-run-commands)可用。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* 从容器映像运行面部容器。
* 分配一个 CPU 核心和 4 GB 的内存。
* 公开 TCP 端口5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` 若要运行容器，则必须指定、和选项，否则容器将无法启动。 有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 `http://localhost:5000`，以获得容器 API。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果在启用了输出[装载](./face-resource-container-config.md#mount-settings)并启用了日志记录的情况下运行容器，则容器将生成日志文件，这些文件有助于排查启动或运行容器时所发生的问题。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

人脸服务容器使用 Azure 帐户中的人脸资源将计费信息发送到 Azure。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

本文介绍了如何下载、安装和运行面部服务容器的概念和工作流。 综上所述：

* 从 Azure 容器注册表下载容器映像。
* 容器映像在 Docker 中运行。
* 你可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用人脸服务容器中的操作。
* 实例化容器时，必须指定计费信息。

> [!IMPORTANT]
> 认知服务容器未获得许可，无需连接到 Azure 进行计量即可运行。 客户必须启用容器，才能始终与计量服务通信计费信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 有关配置设置，请参阅[配置容器](face-resource-container-config.md)。
* 若要了解有关如何检测和识别人脸的详细信息，请参阅人[脸概述](Overview.md)。
* 有关容器支持的方法的信息，请参阅[人脸 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 若要使用更多认知服务容器，请参阅[认知服务容器](../cognitive-services-container-support.md)。
