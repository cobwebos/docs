---
title: How to install and run containers for using Anomaly Detector API
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 的高级算法来确定时序数据中的异常。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327254"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Install and run Anomaly Detector containers (Preview)

The Anomaly Detector has the following container feature functionality:

| 函数 | 功能 |
|--|--|
| Anomaly detector | <li> Detects anomalies as they occur in real-time. <li> Detects anomalies throughout your data set as a batch. <li> Infers the expected normal range of your data. <li> Supports anomaly detection sensitivity adjustment to better fit your data. |

For detailed information about the APIs, please see:
* [Learn more about Anomaly Detector API service](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

You must meet the following prerequisites before using Anomaly Detector containers:

|需要|用途|
|--|--|
|Docker 引擎| 需要在[主计算机](#the-host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。<br><br> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 <br><br> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器。<br><br>|
|熟悉 Docker | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。| 
|Anomaly Detector resource |若要使用这些容器，必须具有：<br><br>An Azure _Anomaly Detector_ resource to get the associated API key and endpoint URI. Both values are available on the Azure portal's **Anomaly Detector** Overview and Keys pages and are required to start the container.<br><br>**{API_KEY}** : One of the two available resource keys on the **Keys** page<br><br>**{ENDPOINT_URI}** : The endpoint as provided on the **Overview** page|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

You must first complete and submit the [Anomaly Detector Container Request form](https://aka.ms/adcontainer) to request access to the container.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>主计算机

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

The following table describes the minimum and recommended CPU cores and memory to allocate for Anomaly Detector container.

| QPS(Queries per second) | 最小值 | 推荐 |
|-----------|---------|-------------|
| 10 QPS | 4 core, 1-GB memory | 8 core 2-GB memory |
| 20 QPS | 8 core, 2-GB memory | 16 core 4-GB memory |

每个核心必须至少为 2.6 千兆赫（GHz）或更快。

核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

## <a name="get-the-container-image-with-docker-pull"></a>使用 `docker pull` 获取容器映像

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令下载容器映像。

| 容器 | 存储库 |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker pull for the Anomaly Detector container

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>如何使用容器

一旦容器位于[主计算机](#the-host-computer)上，请通过以下过程使用容器。

1. 使用所需的计费设置[运行容器](#run-the-container-with-docker-run)。 提供 `docker run` 命令的多个[示例](anomaly-detector-container-configuration.md#example-docker-run-commands)。
1. [查询容器的预测终结点](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>通过 `docker run` 运行容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令运行容器。 Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{ENDPOINT_URI}` and `{API_KEY}` values.

[Examples](anomaly-detector-container-configuration.md#example-docker-run-commands) of the `docker run` command are available.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

此命令：

* Runs an Anomaly Detector container from the container image
* 分配一个 CPU 核心和 4 千兆字节 (GB) 的内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。 

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

### <a name="running-multiple-containers-on-the-same-host"></a>Running multiple containers on the same host

若要运行多个公开端口的容器，请确保在运行每个容器时使用不同的端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

请将 `<container-registry>` 和 `<container-name>` 替换为所用容器的值。 这些容器不需要是同一容器。 You can have the Anomaly Detector container and the LUIS container running on the HOST together or you can have multiple Anomaly Detector containers running. 

在端口 5000 上运行第一个容器。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

在端口 5001 上运行第二个容器。


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

每个后续容器应位于不同的端口上。 

## <a name="query-the-containers-prediction-endpoint"></a>查询容器的预测终结点

容器提供了基于 REST 的查询预测终结点 API。 

使用主机 http://localhost:5000 ，以获得容器 API。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>故障排除

如果运行启用了输出[装入点](anomaly-detector-container-configuration.md#mount-settings)和日志记录的容器，该容器会生成有助于排查启动或运行容器时发生的问题的日志文件。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>计费

The Anomaly Detector containers send billing information to Azure, using an _Anomaly Detector_ resource on your Azure account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

有关这些选项的详细信息，请参阅[配置容器](anomaly-detector-container-configuration.md)。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>总结

In this article, you learned concepts and workflow for downloading, installing, and running Anomaly Detector containers. 摘要：

* Anomaly Detector provides one Linux container for Docker, encapsulating anomaly detection with batch vs streaming, expected range inference, and sensitivity tuning.
* Container images are downloaded from a private Azure Container Registry dedicated for containers preview.
* 容器映像在 Docker 中运行。
* You can use either the REST API or SDK to call operations in Anomaly Detector containers by specifying the host URI of the container.
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 Cognitive Services containers do not send customer data (e.g., the time series data that is being analyzed) to Microsoft.

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](anomaly-detector-container-configuration.md)了解配置设置
* [Deploy an Anomaly Detector container to Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Learn more about Anomaly Detector API service](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
