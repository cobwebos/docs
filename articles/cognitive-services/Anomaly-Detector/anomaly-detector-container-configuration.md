---
title: 如何为异常检测器 API 配置容器
titleSuffix: Azure Cognitive Services
description: 使用`docker run`命令参数配置异常检测器 API 容器运行时环境。 此容器有多个必需设置，以及一些可选设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: f7e04a16fa35d492b8e5e6c53a05220e8b96a38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795848"
---
# <a name="configure-anomaly-detector-containers"></a>配置异常检测器容器

使用`docker run`命令参数配置**异常检测器**容器运行时环境。 此容器有多个必需设置，以及一些可选设置。 多个[示例](#example-docker-run-commands)命令均可用。 容器专用设置是帐单设置。 

## <a name="configuration-settings"></a>配置设置

此容器具有以下配置设置：

|必选|设置|目的|
|--|--|--|
|是|[ApiKey](#apikey-configuration-setting)|用于跟踪账单信息。|
|否|[应用洞察](#applicationinsights-setting)|允许向容器添加 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遥测支持。|
|是|[计费](#billing-configuration-setting)|指定 Azure 上服务资源的终结点 URI。|
|是|[Eula](#eula-setting)| 表示已接受容器的许可条款。|
|否|[Fluentd](#fluentd-settings)|将日志和（可选）指标数据写入 Fluentd 服务器。|
|否|[Http Proxy](#http-proxy-credentials-settings)|配置 HTTP 代理以发出出站请求。|
|否|[Logging](#logging-settings)|为容器提供 ASP.NET Core 日志记录支持。 |
|否|[Mounts](#mount-settings)|从主计算机读取数据并将其写入到容器，以及从容器读回数据并将其写回到主计算机。|

> [!IMPORTANT]
> [`Billing`](#billing-configuration-setting)[`Eula`](#eula-setting)和[`ApiKey`](#apikey-configuration-setting)设置一起使用，您必须为所有三个设置提供有效值;因此，您必须为所有三个设置提供有效值。否则您的容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](anomaly-detector-container-howto.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 您必须为 ApiKey 指定值，并且该值必须是为[`Billing`](#billing-configuration-setting)配置设置指定的_异常检测器_资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：**异常检测器**的资源管理，在**密钥**下

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

该`Billing`设置指定 Azure 上的_异常检测器_资源的终结点 URI，用于计量容器的计费信息。 您必须为此配置设置指定值，并且该值必须是 Azure 上_异常检测器_资源的有效终结点 URI。

可以在以下位置找到此设置：

* Azure 门户：**异常检测器的**概述，标记为`Endpoint`

|必选| “属性” | 数据类型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 计费终结点 URI。 有关获取计费 URI 的详细信息，请参阅[收集所需的参数](anomaly-detector-container-howto.md#gathering-required-parameters)。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](../cognitive-services-custom-subdomains.md)。 |

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http 代理凭据设置

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>日志记录设置
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 选项来指定输入装载或输出装载。

异常检测器容器不使用输入或输出装载来存储训练或服务数据。 

主机确切语法的安装位置因主机操作系统不同而异。 另外，由于 Docker 服务帐户使用的权限与主机装载位置权限之间有冲突，因此可能无法访问[主计算机](anomaly-detector-container-howto.md#the-host-computer)的装载位置。 

|可选| “属性” | 数据类型 | 描述 |
|-------|------|-----------|-------------|
|不允许| `Input` | String | 异常检测器容器不使用此容器。|
|可选| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括容器日志。 <br><br>示例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 运行命令示例 

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](anomaly-detector-container-howto.md#stop-the-container)它。

* **行延续字符**：以下部分中的 Docker 命令使用反斜杠 ，`\`作为 bash shell 的行延续字符。 根据主机操作系统的要求替换或删除字符。 例如，Windows 的行继续符是插入符号 `^`。 将反斜杠替换为插入符号。 
* **参数顺序**：除非非常熟悉 Docker 容器，否则不要更改参数的顺序。

将括号`{}`中的值替换为您自己的值：

| 占位符 | “值” | 格式或示例 |
|-------------|-------|---|
| **[API_KEY]** | “Azure `Anomaly Detector` 密钥”页上的 `Anomaly Detector` 资源的终结点密钥。 | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Azure `Anomaly Detector`“概览”页面上提供了账单终结点值。| 有关显式示例，请参阅[收集所需参数](anomaly-detector-container-howto.md#gathering-required-parameters)。 |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](anomaly-detector-container-howto.md#billing)。
> ApiKey 值是 Azure 异常检测器资源键页中的 **"键**"。 

## <a name="anomaly-detector-container-docker-examples"></a>异常检测器容器 Docker 示例

以下 Docker 示例适用于异常检测器容器。 

### <a name="basic-example"></a>基本示例 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>使用命令行参数的日志记录示例

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>后续步骤

* [将异常检测器容器部署到 Azure 容器实例](how-to/deploy-anomaly-detection-on-container-instances.md)
* [了解有关异常检测器 API 服务的更多信息](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
