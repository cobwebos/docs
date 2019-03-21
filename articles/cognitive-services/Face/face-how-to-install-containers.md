---
title: 安装、运行容器
titlesuffix: Face - Azure Cognitive Services
description: 通过本演练教程了解如何下载、安装和运行“人脸”容器。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 03/19/2019
ms.author: diberry
ms.openlocfilehash: f3534f3001de1c3e58f0be3fb7bc9639b7dfcd03
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295414"
---
# <a name="install-and-run-face-containers"></a>安装并运行人脸容器

人脸识别服务提供适用于 Docker 的标准化 Linux 容器。该容器名为“人脸”，可检测图像中的人脸和识别属性，包括人脸特征（例如，鼻子和眼睛）、性别、年龄和其他计算机预测的面部特征。 除检测外，“人脸”还可以使用置信评分检查同一/不同图像中的两张人脸，或根据数据库比较人脸，以查看是否已存在类似或相同的人脸。 还可以使用共享视觉特征将类似人脸整理为许多组。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

使用人脸 API 容器之前，必须满足以下先决条件：

|需要|目的|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|人脸 API 资源 |若要使用容器，必须具有：<br><br>_人脸 API_ Azure 资源，用于获取关联的计费密钥和计费终结点 URI。 这两个值可以从 Azure 门户中的“人脸 API 概述”和“密钥”页面获得，并且是启动容器时所必需的。<br><br>**{BILLING_KEY}**：资源密钥<br><br>**{BILLING_ENDPOINT_URI}**：终结点 URI 示例如下：`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>主计算机

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表显示了为每个人脸 API 容器分配的最小和建议的 CPU 核心数和内存。

| 容器 | 最小值 | 建议 | TPS<br>（最低配置，最大值）|
|-----------|---------|-------------|--|
|人脸 | 单核，2 GB 内存 | 单核，4 GB 内存 |10, 20|

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。
* TP-每秒事务数

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

提供了有关人脸 API 的容器映像。 

| 容器 | 存储库 |
|-----------|------------|
| 人脸 | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>适用于人脸容器的 Docker 拉取

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行三个容器中的任意一个。 该命令使用以下参数：

| 占位符 | 值 |
|-------------|-------|
|{BILLING_KEY} | 此密钥用于启动容器，可以从 Azure 门户中的“人脸 API 密钥”页上获取该密钥。  |
|{BILLING_ENDPOINT_URI} | 可以从 Azure 门户中的“人脸 API 概述”页上获得计费终结点 URI 值。|

在以下示例 `docker run` 命令中，请将这些参数替换为自己的值。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

此命令：

* 从容器映像运行人脸容器
* 分配一个 CPU 核心和 4 千兆字节 (GB) 的内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

提供 `docker run` 命令的多个[示例](./face-resource-container-config.md#example-docker-run-commands)。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 `https://localhost:5000`，以获得容器 API。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](./face-resource-container-config.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。 

## <a name="containers-api-documentation"></a>容器的 API 文档

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>计费

人脸 API 容器使用 Azure 帐户中的_人脸 API_ 资源向 Azure 发送账单信息。 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](./face-resource-container-config.md)。

## <a name="summary"></a>摘要

在本文中，我们已了解相关的概念，以及人脸 API 容器的下载、安装和运行工作流。 综上所述：

* 人脸 API 提供三个适用于 Docker 的 Linux 容器，用于封装关键短语提取、语言检测和情绪分析。
* 从 Azure 中的 Microsoft 容器注册表 (MCR) 下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用人脸 API 容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](face-resource-container-config.md)了解配置设置
* 查看[“人脸”概述](Overview.md)，详细了解如何检测和识别人脸  
* 有关该容器支持的方法的详细信息，请参阅[人脸 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 参阅[常见问题解答 (FAQ)](FAQ.md) 解决与“人脸”功能相关的问题。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
