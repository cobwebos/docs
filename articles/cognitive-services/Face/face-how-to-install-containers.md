---
title: 安装和运行容器
titlesuffix: Face - Azure Cognitive Services
description: 下载、 安装和运行为人脸在此演练教程中的容器。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 4d3f3e64e109d0b1dc010be39b62b4f0bdc0573d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063600"
---
# <a name="install-and-run-face-containers"></a>安装并运行人脸容器

Azure 认知服务人脸检测的人脸在图像中的 docker 提供标准化的 Linux 容器。 它还标识属性，其中包括如鼻子下面和眼睛、 性别、 年龄和其他机器预测面部识别功能的人脸特征点。 除了检测，人脸可以检查在相同的映像或不同的映像中的两张脸是否相同使用置信度得分。 人脸还可以比较针对一个数据库以查看是否已存在的相似或相同的人脸的人脸。 它还可以将相似的人脸分组使用共享的视觉特征。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

在使用人脸 API 容器之前，必须满足以下先决条件。

|需要|目的|
|--|--|
|Docker 引擎| 必须在上安装 Docker 引擎[主机计算机](#the-host-computer)。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 中，Docker 还必须配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 需要基本了解 Docker 概念，例如注册表、 存储库、 容器和容器映像。 您还需的基本知识`docker`命令。| 
|Azure`Cognitive Services`资源 |若要使用容器，必须具有：<br><br>Azure 认知服务资源和关联的帐单密钥和计费的终结点 URI。 这两个值位于**概述**并**密钥**资源页。 它们需要启动该容器。 添加`face/v1.0`路由到终结点 URI，如下面的 BILLING_ENDPOINT_URI 示例中所示： <br><br>**{BILLING_KEY}** ：资源密钥<br><br>**{BILLING_ENDPOINT_URI}** ： 终结点的 URI 示例是 `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表显示了为每个人脸 API 容器分配的最小和建议的 CPU 核心数和内存。

| 容器 | 最小值 | 建议 | 每秒事务数<br>（最低配置，最大）|
|-----------|---------|-------------|--|
|人脸 | 单核，2 GB 内存 | 单核，4 GB 内存 |10, 20|

* 每个核心必须是至少 2.6 GHz 或速度更快。
* 每秒事务数 (TPS)。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>获取与 docker 拉取容器映像

提供了有关人脸 API 的容器映像。 

| 容器 | 存储库 |
|-----------|------------|
| 人脸 | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>适用于人脸容器的 Docker 拉取

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>使用容器

在容器上后[主机计算机](#the-host-computer)，使用以下过程来使用该容器。

1. [运行容器](#run-the-container-with-docker-run)必选计费设置。 提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>使用运行的 docker 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行三个容器中的任意一个。 该命令使用以下参数。

| 占位符 | 值 |
|-------------|-------|
|{BILLING_KEY} | 此密钥用于启动该容器，可在 Azure 上`Cognitive Services`**密钥**页。 |
|{BILLING_ENDPOINT_URI} | 计费终结点 URI 值是可在 Azure 上`Cognitive Services`**概述**页。 例如 `https://westus.api.cognitive.microsoft.com/face/v1.0`。|

添加`face/v1.0`路由到终结点 URI，如前面的 BILLING_ENDPOINT_URI 示例中所示。 

这些参数替换为你自己的值在下面的示例`docker run`命令示例：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 从容器映像中运行的人脸容器。
* 分配一个 CPU 核心和 4 GB 的内存。
* 公开 TCP 端口 5000，并为容器分配伪 TTY。
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> `Eula`， `Billing`，和`ApiKey`选项必须指定要运行该容器或容器不会启动。 有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 `https://localhost:5000`，以获得容器 API。


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果使用输出运行容器[装载](./face-resource-container-config.md#mount-settings)和启用日志记录，该容器生成有助于排查问题发生时启动或运行容器的日志文件。 


## <a name="billing"></a>计费

人脸 API 容器在你的 Azure 帐户使用人脸 API 资源将计费信息发送到 Azure。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./face-resource-container-config.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>摘要

在本文中，你学习概念以及如何下载、 安装并运行人脸 API 容器的工作流。 综上所述：

* 人脸 API 为 Docker 提供关键短语提取、 语言检测和情绪分析提供了三个 Linux 容器。
* 从 Azure 容器注册表下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 调用人脸 API 容器中的操作通过指定主机的容器的 URI。
* 实例化一个容器时，必须指定计费信息。

> [!IMPORTANT]
> 认知服务容器未获得许可，无需连接到 Azure 计数的运行。 客户必须使容器能够通信与计量服务在所有时间的计费信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 有关配置设置，请参阅[配置容器](face-resource-container-config.md)。
* 若要了解有关如何检测和识别人脸的详细信息，请参阅[人脸概述](Overview.md)。
* 有关支持容器的方法的信息，请参阅[人脸 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 若要使用更多的认知服务容器，请参阅[认知服务容器](../cognitive-services-container-support.md)。
