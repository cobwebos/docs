---
title: Install and run containers - FACE API
titleSuffix: Azure Cognitive Services
description: This article shows you how to download, install, and run containers for Face in this walkthrough tutorial.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 574f6bead9cac384c72d2d0cd35353eb571a9490
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327040"
---
# <a name="install-and-run-face-containers-preview"></a>Install and run Face containers (Preview)

Azure Cognitive Services Face provides a standardized Linux container for Docker that detects human faces in images. It also identifies attributes, which include face landmarks such as noses and eyes, gender, age, and other machine-predicted facial features. In addition to detection, Face can check if two faces in the same image or different images are the same by using a confidence score. Face also can compare faces against a database to see if a similar-looking or identical face already exists. It also can organize similar faces into groups by using shared visual traits.

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

You must meet the following prerequisites before you use the Face API containers.

|需要|用途|
|--|--|
|Docker 引擎| The Docker Engine must be installed on a [host computer](#the-host-computer). Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> On Windows, Docker also must be configured to support Linux containers.<br><br>|
|熟悉 Docker | You need a basic understanding of Docker concepts, such as registries, repositories, containers, and container images. You also need knowledge of basic `docker` commands.| 
|Face resource |To use the container, you must have:<br><br>An Azure **Face** resource and the associated API key and the endpoint URI. Both values are available on the **Overview** and **Keys** pages for the resource. They're required to start the container.<br><br>**{API_KEY}** : One of the two available resource keys on the **Keys** page<br><br>**{ENDPOINT_URI}** : The endpoint as provided on the **Overview** page

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表显示了为每个人脸 API 容器分配的最小和建议的 CPU 核心数和内存。

| 容器 | 最小值 | 推荐 | 事务数/秒<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|人脸 | 1 core, 2-GB memory | 1 core, 4-GB memory |10, 20|

* Each core must be at least 2.6 GHz or faster.
* Transactions per second (TPS).

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>Get the container image with docker pull

Container images for the Face API are available. 

| 容器 | 存储库 |
|-----------|------------|
| 人脸 | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>适用于人脸容器的 Docker 拉取

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Use the container

当容器位于[主计算机](#the-host-computer)上以后，请通过以下过程使用容器。

1. [Run the container](#run-the-container-with-docker-run) with the required billing settings. 提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>Run the container with docker run

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{ENDPOINT_URI}` and `{API_KEY}` values.

[Examples](face-resource-container-config.md#example-docker-run-commands) of the `docker run` command are available.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* Runs a face container from the container image.
* Allocates one CPU core and 4 GB of memory.
* Exposes TCP port 5000 and allocates a pseudo TTY for the container.
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> The `Eula`, `Billing`, and `ApiKey` options must be specified to run the container or the container won't start. 有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 `http://localhost:5000`，以获得容器 API。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

If you run the container with an output [mount](./face-resource-container-config.md#mount-settings) and logging is enabled, the container generates log files that are helpful to troubleshoot issues that happen while you start or run the container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

The Face API containers send billing information to Azure by using a Face API resource on your Azure account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>总结

In this article, you learned concepts and workflow for how to download, install, and run Face API containers. 摘要：

* Container images are downloaded from the Azure Container Registry.
* 容器映像在 Docker 中运行。
* You can use either the REST API or the SDK to call operations in Face API containers by specifying the host URI of the container.
* You must specify billing information when you instantiate a container.

> [!IMPORTANT]
> Cognitive Services containers aren't licensed to run without being connected to Azure for metering. Customers must enable the containers to communicate billing information with the metering service at all times. 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* For configuration settings, see [Configure containers](face-resource-container-config.md).
* To learn more about how to detect and identify faces, see [Face overview](Overview.md).
* For information about the methods supported by the container, see the [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* To use more Cognitive Services containers, see [Cognitive Services containers](../cognitive-services-container-support.md).
