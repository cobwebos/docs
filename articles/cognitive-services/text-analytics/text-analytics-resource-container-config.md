---
title: 配置容器 - 文本分析
titleSuffix: Azure Cognitive Services
description: 文本分析为每个容器提供一个通用配置框架，以便可以轻松配置和管理容器的存储、日志记录、遥测和安全性设置。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f062fb2f3a653bc1b2845b92e373fdb67ba583d8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878646"
---
# <a name="configure-text-analytics-docker-containers"></a>配置文本分析 docker 容器

文本分析为每个容器提供一个通用配置框架，以便可以轻松配置和管理容器的存储、日志记录、遥测和安全性设置。

## <a name="configuration-settings"></a>配置设置

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`Billing`](#billing-configuration-setting)[`Eula`](#eula-setting)和[`ApiKey`](#apikey-configuration-setting)设置一起使用，您必须为所有三个设置提供有效值;因此，您必须为所有三个设置提供有效值。否则您的容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](how-tos/text-analytics-how-to-install-containers.md#billing)。

## <a name="apikey-configuration-setting"></a>ApiKey 配置设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，并且该值必须是为 [`Billing`](#billing-configuration-setting) 配置设置指定的_文本分析_资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：**文本分析**资源管理，在**密钥**下

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 配置设置

该`Billing`设置指定 Azure 上用于计量容器计费信息_的文本分析_资源的终结点 URI。 您必须为此配置设置指定值，并且该值必须是 Azure 上 @_文本分析_资源的有效终结点 URI。 容器约每 10 到 15 分钟报告一次使用情况。

可以在以下位置找到此设置：

* Azure 门户：**文本分析**概述，已标记`Endpoint`

|必选| 名称 | 数据类型 | 说明 |
|--|------|-----------|-------------|
|是| `Billing` | 字符串 | 计费终结点 URI。 有关获取计费 URI 的详细信息，请参阅[收集所需的参数](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](../cognitive-services-custom-subdomains.md)。 |

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

主机确切语法的安装位置因主机操作系统不同而异。 此外，由于 Docker 服务帐户使用的权限与主机装载位置权限之间的冲突，[主机](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)的装载位置可能无法访问。 

|可选| 名称 | 数据类型 | 说明 |
|-------|------|-----------|-------------|
|不允许| `Input` | 字符串 | 文本分析容器不使用此项。|
|可选| `Output` | 字符串 | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括容器日志。 <br><br>示例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Docker 运行命令示例 

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)它。

* **行延续字符**：以下部分中的 docker 命令使用反斜杠 ，`\`作为行延续字符。 根据主机操作系统的要求替换或删除字符。 
* **参数顺序**：除非非常熟悉 docker 容器，否则不要更改参数的顺序。

将 {_argument_name_} 替换为为你自己的值：

| 占位符 | 值 | 格式或示例 |
|-------------|-------|---|
| **[API_KEY]** | Azure `Text Analytics`“密钥”页上提供的 `Text Analytics` 资源的终结点密钥。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Azure `Text Analytics`“概览”页面上提供了账单终结点值。| 有关显式示例，请参阅[收集所需参数](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters)。 |

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](how-tos/text-analytics-how-to-install-containers.md#billing)。
> ApiKey 值是来自 Azure `Text Analytics`“资源密钥”页的“密钥”****。 

#### <a name="key-phrase-extraction"></a>[关键短语提取](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[语言检测](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[情绪分析](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](how-tos/text-analytics-how-to-install-containers.md)
* 使用更多[认知服务容器](../cognitive-services-container-support.md)
