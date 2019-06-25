---
title: 配置容器
titlesuffix: Text Analytics - Azure Cognitive Services
description: 文本分析为每个容器提供一个通用配置框架，以便可以轻松配置和管理容器的存储、日志记录、遥测和安全性设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: dapine
ms.openlocfilehash: 604b17bc3d4696ff0bf2be1759a53927a531fe13
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296023"
---
# <a name="configure-text-analytics-docker-containers"></a>配置文本分析 docker 容器

文本分析为每个容器提供一个通用配置框架，以便可以轻松配置和管理容器的存储、日志记录、遥测和安全性设置。

## <a name="configuration-settings"></a>配置设置

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting) 和 [`Eula`](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](how-tos/text-analytics-how-to-install-containers.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，且此值必须是为 [`Billing`](#billing-configuration-setting) 配置设置指定的“认知服务”  资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：**认知服务**“资源管理”部分的“密钥”下 

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

`Billing` 设置指定 Azure 上用于计量容器帐单信息的“认知服务”  资源的终结点 URI。 必须为这个配置设置指定值，且此值必须是 Azure 上“认知服务”  资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

可以在以下位置找到此设置：

* Azure 门户：**认知服务**概述，标记为 `Endpoint`

需将 `text/analytics/v2.0` 路由添加到终结点 URI，如以下 BILLING_ENDPOINT_URI 示例所示。

|必选| Name | 数据类型 | 描述 |
|--|------|-----------|-------------|
|是| `Billing` | String | 账单终结点 URI<br><br>示例：<br>`Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.1` |

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

文本分析容器不使用输入或输出装载来存储训练或服务数据。 

主机确切语法的安装位置因主机操作系统不同而异。 此外，由于 docker 服务帐户使用的权限与主机安装位置权限之间的冲突，可能无法访问[主计算机](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)的装载位置。 

|可选| Name | 数据类型 | 描述 |
|-------|------|-----------|-------------|
|不允许| `Input` | String | 文本分析容器不使用此项。|
|可选| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括容器日志。 <br><br>示例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 运行命令示例 

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)它。

* **行继续符**：以下各节中的 docker 命令使用反斜杠 `\` 作为行继续符。 根据主机操作系统的要求替换或删除字符。 
* **参数顺序**：除非非常熟悉 docker 容器，否则不要更改参数顺序。

需将 `text/analytics/v2.0` 路由添加到终结点 URI，如以下 BILLING_ENDPOINT_URI 示例所示。

将 {_argument_name_} 替换为为你自己的值：

| 占位符 | 值 | 格式或示例 |
|-------------|-------|---|
|{BILLING_KEY} | Azure `Cognitive Services`“密钥”页上提供的 `Cognitive Services` 资源的终结点密钥。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Azure `Cognitive Services`“概览”页面上提供了账单终结点值。|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](how-tos/text-analytics-how-to-install-containers.md#billing)。
> ApiKey 值是来自 Azure `Cognitive Services`“资源密钥”页的“密钥”  。 

## <a name="key-phrase-extraction-container-docker-examples"></a>关键短语提取容器 docker 示例

以下 docker 示例适用于关键短语提取容器。 

### <a name="basic-example"></a>基本示例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>日志记录示例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>语言检测容器 docker 示例

以下 docker 示例适用于语言检测容器。 

### <a name="basic-example"></a>基本示例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>日志记录示例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>情绪分析容器 docker 示例

以下 docker 示例适用于情绪分析容器。 

### <a name="basic-example"></a>基本示例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example"></a>日志记录示例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](how-tos/text-analytics-how-to-install-containers.md)
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
