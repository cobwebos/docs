---
title: 为人脸 API 安装并运行 Docker 容器
titleSuffix: Azure Cognitive Services
description: 使用人脸 API 的 Docker 容器来检测和识别图像中的人脸。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
keywords: 本地、Docker、容器、标识
ms.openlocfilehash: 0f6807f771510f85c5a20cfb2a160cfe1e8726a3
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461506"
---
# <a name="install-and-run-face-containers-preview"></a>安装和运行人脸容器（预览版）

> [!IMPORTANT]
> 已达到人脸容器用户的限制。 目前不接受用于人脸容器的新应用程序。

Azure 认知服务人脸 API 提供了一个 Linux Docker 容器，用于检测和分析图像中的人脸。 它还标识属性，其中包括人脸特征（例如，鼻子和眼睛）、性别、年龄和其他计算机预测的面部特征。 除检测外，人脸还可以使用置信分数检查同一图像或不同图像中的两张人脸是否相同。 人脸还可以根据数据库比较人脸，查看是否已存在类似或相同的人脸。 它也可以使用共享视觉特征将类似人脸整理为许多组。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

使用人脸服务容器之前，必须满足以下先决条件：

|必须|目的|
|--|--|
|Docker 引擎| 必须在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 需对 Docker 概念（例如注册表、存储库、容器和容器映像）有一个基本的理解。 还需要了解基本的 `docker` 命令。| 
|人脸资源 |若要使用容器，必须具有：<br><br>Azure 人脸资源和关联的 API 密钥及终结点 URI。 可在资源的“概述”和“密钥”页上获取这两个值 。 必须获取这两个值才能启动容器。<br><br>**{API_KEY}** ：“密钥”页上提供的两个可用资源密钥中的一个****<br><br>**{ENDPOINT_URI}** ：“概述”页上提供的终结点****

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表显示了为每个人脸服务容器分配的最小和建议的 CPU 核心数和内存。

| 容器 | 最小值 | 建议 | 每秒事务数<br>（最小值、最大值）|
|-----------|---------|-------------|--|
|人脸 | 单核，2-GB 内存 | 单核，4-GB 内存 |10, 20|

* 每个核心必须至少为 2.6 GHz 或更快。
* 每秒事务数 (TPS)。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 docker pull 获取容器映像

提供了适用于人脸服务的容器映像。 

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

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>通过 docker run 运行容器

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

* 从容器映像运行人脸容器。
* 分配一个 CPU 核心和 4 GB 内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器，否则该容器不会启动。 有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

为容器 API 使用主机 `http://localhost:5000`。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装载](./face-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

人脸服务容器使用 Azure 帐户中的人脸资源向 Azure 发送账单信息。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

本文介绍了与下载、安装和运行人脸服务容器相关的概念和工作流。 综上所述：

* 从 Azure 容器注册表下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用人脸服务容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户必须始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 有关配置设置，请参阅[配置容器](face-resource-container-config.md)。
* 若要详细了解如何检测和识别人脸，请参阅[人脸概述](Overview.md)。
* 有关该容器支持的方法的信息，请参阅[人脸 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 若要使用更多认知服务容器，请参阅[认知服务容器](../cognitive-services-container-support.md)。
