---
title: Docker 容器设置
titleSuffix: Language Understanding - Azure Cognitive Services
description: 使用 `docker run` 命令参数配置 LUIS 容器运行时环境。 LUIS 有几个必需的设置以及一些可选设置。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: e8e838fae0da3a47fe1b3ec8d412f956f5f28034
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975503"
---
# <a name="configure-language-understanding-docker-containers"></a>配置语言理解 docker 容器 

语言理解 (LUIS) 容器运行时环境使用 `docker run` 命令参数进行配置。 LUIS 有几个必需的设置以及一些可选设置。 多个[示例](#example-docker-run-commands)命令均可用。 特定于容器的设置是输入[装载设置](#mount-settings)和账单设置。 

容器设置[分层](#hierarchical-settings)，可以使用[环境变量](#environment-variable-settings)或 docker [命令行参数](#command-line-argument-settings)进行设置。

## <a name="configuration-settings"></a>配置设置

此容器具有以下配置设置：

|必选|设置|目的|
|--|--|--|
|是|[ApiKey](#apikey-setting)|用于跟踪账单信息。|
|否|[ApplicationInsights](#applicationinsights-setting)|允许向容器添加 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遥测支持。|
|是|[计费](#billing-setting)|指定 Azure 上服务资源的终结点 URI。|
|是|[Eula](#eula-setting)| 表示已接受容器的许可条款。|
|否|[Fluentd](#fluentd-settings)|将日志和（可选）指标数据写入 Fluentd 服务器。|
|否|[日志记录](#logging-settings)|为容器提供 ASP.NET Core 日志记录支持。 |
|是|[Mounts](#mount-settings)|从主计算机读取数据并将其写入到容器，以及从容器读回数据并将其写回到主计算机。|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)、[`Billing`](#billing-setting) 和 [`Eula`](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](luis-container-howto.md#billing)。

## <a name="apikey-setting"></a>ApiKey 设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，并且该值必须是为 [`Billing`](#billing-setting) 配置设置指定的语言理解资源的有效密钥。

可以在以下位置找到此设置：

* Azure 门户：语言理解的“资源管理”，在“密钥”下
* LUIS 门户：“密钥和终结点”设置页。 

请勿使用初学者密钥或创作密钥。 

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>账单设置

`Billing` 设置指定 Azure 上用于计量容器账单信息的语言理解资源的终结点 URI。 必须为此配置设置指定值，并且该值必须是 Azure 上语言理解资源的有效终结点 URI。

可以在以下位置找到此设置：

* Azure 门户：语言理解的“概述”，标记为 `Endpoint`
* LUIS 门户：“密钥和终结点设置”页面，作为终结点 URI 的一部分。

|必选| 名称 | 数据类型 | Description |
|--|------|-----------|-------------|
|是| `Billing` | String | 账单终结点 URI<br><br>示例：<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Eula 设置

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd 设置


[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="logging-settings"></a>日志记录设置
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>装载设置

使用绑定装载从容器读取数据并将数据写入容器。 可以通过在 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令中指定 `--mount` 选项来指定输入装载或输出装载。 

LUIS 容器不使用输入或输出装载来存储训练或服务数据。 

主机确切语法的安装位置因主机操作系统不同而异。 此外，由于 docker 服务帐户使用的权限与主机安装位置权限之间的冲突，可能无法访问[主计算机](luis-container-howto.md#the-host-computer)的装载位置。 

下表描述了支持的设置。

|必选| 名称 | 数据类型 | Description |
|-------|------|-----------|-------------|
|是| `Input` | String | 输入装入点的目标。 默认值为 `/input`。 这是 LUIS 包文件的位置。 <br><br>示例：<br>`--mount type=bind,src=c:\input,target=/input`|
|否| `Output` | String | 输出装入点的目标。 默认值为 `/output`。 这是日志的位置。 这包括 LUIS 查询日志和容器日志。 <br><br>示例：<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>分层设置

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]


## <a name="example-docker-run-commands"></a>Docker 运行命令示例

以下示例使用的配置设置说明如何编写和使用 `docker run` 命令。  运行后，容器将继续运行，直到[停止](luis-container-howto.md#stop-the-container)它。


* **行继续符**：以下各节中的 docker 命令使用反斜杠 `\` 作为行继续符。 根据主机操作系统的要求替换或删除字符。 
* **参数顺序**：除非非常熟悉 docker 容器，否则不要更改参数顺序。

将 {_argument_name_} 替换为为你自己的值：

| 占位符 | 值 | 格式或示例 |
|-------------|-------|---|
|{ENDPOINT_KEY} | 已训练 LUIS 应用程序的终结点密钥。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | Azure 门户的“语言理解概述”页面上提供了计费终结点值。|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](luis-container-howto.md#billing)。
> ApiKey 值是 LUIS 门户中“密钥和终结点”页面中的“密钥”，也可以在 Azure 语言理解资源密钥页上找到。 

### <a name="basic-example"></a>基本示例

以下示例具有运行容器的最少参数：

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> 上述命令使用 `c:` 驱动器下的目录来避免 Windows 上的任何权限冲突。 如果需要使用特定目录作为输入目录，则需要授予 docker 服务权限。 前面的 docker 命令使用反斜杠 `\` 作为行继续符。 根据[主计算机](luis-container-howto.md#the-host-computer)操作系统的要求替换或删除此字符。 除非非常熟悉 docker 容器，否则不要更改参数顺序。


### <a name="applicationinsights-example"></a>ApplicationInsights 示例

以下示例设置 ApplicationInsights 参数，以便在容器运行时将遥测数据发送到 Application Insights：

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>使用命令行参数的日志记录示例

以下命令设置日志记录级别 `Logging:Console:LogLevel`，将日志记录级别配置为 [`Information`](https://msdn.microsoft.com)。 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>带有环境变量的日志记录示例

以下命令使用名为 `Logging:Console:LogLevel` 的环境变量将日志记录级别配置为 [`Information`](https://msdn.microsoft.com)。 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>后续步骤

* 查看[如何安装和运行容器](luis-container-howto.md)
* 参阅[常见问题解答 (FAQ)](luis-resources-faq.md) 解决与 LUIS 功能相关的问题。