---
title: 安装和运行容器 - 面
titleSuffix: Azure Cognitive Services
description: 本文在本演练教程中演示如何下载、安装和运行 Face 容器。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165982"
---
# <a name="install-and-run-face-containers-preview"></a>安装和运行面容器（预览）

Azure 认知服务面为 Docker 提供了一个标准化的 Linux 容器，用于检测图像中的人脸。 它还识别属性，包括面部地标，如鼻子和眼睛、性别、年龄和其他机器预测的面部特征。 除了检测之外，Face 还可以使用置信度分数检查同一图像中的两个面或不同的图像是否相同。 面还可以将面与数据库进行比较，以查看是否存在相似外观或相同面。 它还可以使用共享的视觉特征将相似的面孔组织成组。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

在使用 Face 服务容器之前，必须满足以下先决条件。

|必选|目的|
|--|--|
|Docker 引擎| Docker 引擎必须安装在[主机](#the-host-computer)上。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 您需要对 Docker 概念（如注册表、存储库、容器和容器映像）的基本了解。 您还需要基本`docker`命令的知识。| 
|人脸资源 |要使用容器，您必须具有：<br><br>Azure**面**资源以及关联的 API 密钥和终结点 URI。 这两个值都可以在资源的 **"概述**"和 **"键"** 页上使用。 他们需要启动容器。<br><br>**[API_KEY]**：**密钥**页上的两个可用资源键之一<br><br>**[ENDPOINT_URI]**：**概述**页上提供的终结点

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表描述了为每个 Face 服务容器分配的最小和推荐的 CPU 内核和内存。

| 容器 | 最小值 | 建议 | 每秒事务数<br>（最小，最大）|
|-----------|---------|-------------|--|
|人脸 | 单核，2-GB 内存 | 单核，4-GB 内存 |10, 20|

* 每个内核必须至少为 2.6 GHz 或更快。
* 每秒事务 （TPS）。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用码头拉取容器映像

面服务的容器映像可用。 

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

## <a name="run-the-container-with-docker-run"></a>使用 Docker 运行运行容器

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

* 从容器映像运行面容器。
* 分配一个 CPU 内核和 4 GB 内存。
* 公开 TCP 端口 5000 并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须`Eula`指定`Billing`和`ApiKey`选项才能运行容器，否则容器将无法启动。 有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 `http://localhost:5000`，以获得容器 API。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>疑难解答

如果启用了输出[装载](./face-resource-container-config.md#mount-settings)和日志记录运行容器，则容器将生成日志文件，这些文件有助于解决启动或运行容器时出现的问题。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

面服务容器通过使用 Azure 帐户上的面资源向 Azure 发送计费信息。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>总结

在本文中，您学习了如何下载、安装和运行 Face 服务容器的概念和工作流。 综上所述：

* 容器映像从 Azure 容器注册表下载。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用 Face 服务容器中的操作。
* 实例化容器时必须指定计费信息。

> [!IMPORTANT]
> 认知服务容器未获得运行许可，无需连接到 Azure 进行计量。 客户必须使容器能够随时与计量服务通信计费信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 有关配置设置，请参阅[配置容器](face-resource-container-config.md)。
* 要了解有关如何检测和识别人脸的详细信息，请参阅[人脸概述](Overview.md)。
* 有关容器支持的方法的信息，请参阅[面 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 要使用更多认知服务容器，请参阅[认知服务容器](../cognitive-services-container-support.md)。
