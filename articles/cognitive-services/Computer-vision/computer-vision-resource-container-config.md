---
title: 配置容器 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 在计算机视觉中为识别文本容器配置各种设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: aba846ade9e2b5e19304df87ea3e29713aacf4ba
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129964"
---
# <a name="configure-computer-vision-docker-containers"></a>配置计算机视觉 Docker 容器

使用`docker run`命令参数配置计算机视觉容器的运行时环境。 此容器有多个必需设置，以及一些可选设置。 多个[示例](#example-docker-run-commands)命令均可用。 容器专用设置是帐单设置。 

## <a name="configuration-settings"></a>配置设置

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](computer-vision-how-to-install-containers.md)。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

此`ApiKey`设置指定用于跟踪`Cognitive Services`容器的计费信息的 Azure 资源密钥。 必须为 ApiKey 指定值，且此值必须是为 [`Billing`](#billing-configuration-setting) 配置设置指定的“认知服务”资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：**认知服务**“资源管理”部分的“密钥”下

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

`Billing` 设置指定 Azure 上用于计量容器帐单信息的“认知服务”资源的终结点 URI。 必须为这个配置设置指定值，且此值必须是 Azure 上“认知服务”资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

可以在以下位置找到此设置：

* Azure 门户：**认知服务**概述，标记为 `Endpoint`

请记住将`vision/v1.0`路由添加到终结点 URI, 如下表所示。 

|必填| 姓名 | 数据类型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 账单终结点 URI<br><br>例如：<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP 代理凭据设置

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 选项来指定输入装载或输出装载。

“计算机视觉”容器不使用输入或输出装载来存储定型或服务数据。 

主机确切语法的安装位置因主机操作系统不同而异。 另外，由于 Docker 服务帐户使用的权限与主机装载位置权限之间有冲突，因此可能无法访问[主计算机](computer-vision-how-to-install-containers.md#the-host-computer)的装载位置。 

|可选| 姓名 | 数据类型 | 描述 |
|-------|------|-----------|-------------|
|不允许| `Input` | String | “计算机视觉”容器不使用此项。|
|可选| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括容器日志。 <br><br>例如：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 运行命令示例

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](computer-vision-how-to-install-containers.md#stop-the-container)它。

* **行继续符**：以下各部分中的 Docker 命令使用反斜杠 `\` 作为行继续符。 根据主机操作系统的要求替换或删除字符。 
* **参数顺序**：除非很熟悉 Docker 容器，否则不要更改参数顺序。

将 {_argument_name_} 替换为为你自己的值：

| 占位符 | ReplTest1 | 格式或示例 |
|-------------|-------|---|
| **{API_KEY}** | `Computer Vision` Azure`Computer Vision`密钥页上的资源的终结点键。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Azure `Computer Vision`“概览”页面上提供了账单终结点值。| 有关显式示例，请参阅[收集所需的参数](computer-vision-how-to-install-containers.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](computer-vision-how-to-install-containers.md#billing)。
> ApiKey 值是来自 Azure `Cognitive Services`“资源密钥”页的“密钥”。

## <a name="container-docker-examples"></a>容器 Docker 示例

#### <a name="readtabread"></a>[读取](#tab/read)

以下 Docker 示例适用于读取容器。

### <a name="basic-example"></a>基本示例

  ```
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>日志记录示例 

  ```
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

#### <a name="recognize-texttabrecognize-text"></a>[识别文本](#tab/recognize-text)

以下 Docker 示例适用于识别文本容器。

### <a name="basic-example"></a>基本示例

  ```
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>日志记录示例

  ```
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

***

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](computer-vision-how-to-install-containers.md)。
