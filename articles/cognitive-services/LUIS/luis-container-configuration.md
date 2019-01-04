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
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080259"
---
# <a name="configure-containers"></a>配置容器

语言理解 (LUIS) 容器运行时环境使用 `docker run` 命令参数进行配置。 LUIS 有几个必需的设置以及一些可选设置。 多个[示例](#example-docker-run-commands)命令均可用。 特定于容器的设置是输入[装载设置](#mount-settings)和账单设置。 

容器设置[分层](#hierarchical-settings)，可以使用[环境变量](#environment-variable-settings)或 docker [命令行参数](#command-line-argument-settings)进行设置。

## <a name="configuration-settings"></a>配置设置

此容器具有以下配置设置：

|必选|设置|目的|
|--|--|--|
|是|[ApiKey](#apikey-setting)|用于跟踪账单信息。|
|否|[ApplicationInsights](#applicationinsights-setting)|允许向容器添加 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遥测支持。|
|是|[计费](#billing-setting)|指定 Azure 上语言了解资源的终结点 URI。|
|是|[Eula](#eula-setting)| 表示已接受容器的许可条款。|
|否|[Fluentd](#fluentd-settings)|将日志和（可选）指标数据写入 Fluentd 服务器。|
|否|[日志记录](#logging-settings)|为容器提供 ASP.NET Core 日志记录支持。 |
|是|[Mounts](#mount-settings)|从[主计算机](luis-container-howto.md#the-host-computer)读取并写入数据到容器，并从容器读取并写入数据到主计算机。|

> [!IMPORTANT]
> [`ApiKey`](#apikey-setting)、[`Billing`](#billing-setting) 和 [`Eula`](#eula-setting) 设置一起使用。必须为所有三个设置提供有效值，否则容器将无法启动。 有关使用这些配置设置实例化容器的详细信息，请参阅[计费](luis-container-howto.md#billing)。

## <a name="apikey-setting"></a>ApiKey 设置

`ApiKey` 设置指定用于跟踪容器账单信息的 Azure 资源键。 必须为 ApiKey 指定值，并且该值必须是为 [`Billing`](#billing-setting) 配置设置指定的语言理解资源的有效密钥。

可以在两个位置找到此设置：

* Azure 门户：语言理解的“资源管理”，在“密钥”下
* LUIS 门户：“密钥和终结点”设置页。 

请勿使用初学者密钥或创作密钥。 

## <a name="applicationinsights-setting"></a>ApplicationInsights 设置

此 `ApplicationInsights` 设置允许向容器添加 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遥测支持。 Application Insights 可深入监视容器。 可以轻松监视容器的可用性、性能和使用情况。 还可以快速识别和诊断容器中的错误。

下表描述了 `ApplicationInsights` 节支持的配置设置。

|必选| 名称 | 数据类型 | Description |
|--|------|-----------|-------------|
|否| `InstrumentationKey` | String | 容器遥测数据要发送到的 Application Insights 实例的检测密钥。 有关详细信息，请参阅[适用于 ASP.NET Core 的 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)。 <br><br>示例：<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>账单设置

`Billing` 设置指定 Azure 上用于计量容器账单信息的语言理解资源的终结点 URI。 必须为此配置设置指定值，并且该值必须是 Azure 上语言理解资源的有效终结点 URI。

可以在两个位置找到此设置：

* Azure 门户：语言理解的“概述”，标记为 `Endpoint`
* LUIS 门户：“密钥和终结点设置”页面，作为终结点 URI 的一部分。

|必选| 名称 | 数据类型 | Description |
|--|------|-----------|-------------|
|是| `Billing` | String | 账单终结点 URI<br><br>示例：<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Eula 设置

`Eula` 设置表示已接受容器的许可条款。 必须为此配置设置指定值，并且该值必须设置为 `accept`。

|必选| 名称 | 数据类型 | Description |
|--|------|-----------|-------------|
|是| `Eula` | String | 接受许可证<br><br>示例：<br>`Eula=accept` |

根据管理 Azure 使用的[协议](https://go.microsoft.com/fwlink/?linkid=2018657)授予认知服务容器许可。 如果没有管理 Azure 使用的现有协议，则表示同意管理 Azure 使用的协议是 [Microsoft 在线订阅协议](https://go.microsoft.com/fwlink/?linkid=2018755)，其中包含[联机服务条款](https://go.microsoft.com/fwlink/?linkid=2018760)。 对于预览版，还应同意 [Microsoft Azure 预览版补充使用条款](https://go.microsoft.com/fwlink/?linkid=2018815)。 使用容器即表示同意这些条款。

## <a name="fluentd-settings"></a>Fluentd 设置

Fluentd 是一个用于统一日志记录的开放源代码数据收集器。 `Fluentd` 设置管理容器到 [Fluentd](https://www.fluentd.org) 服务器的连接。 LUIS 容器包含一个 Fluentd 日志记录提供程序，让容器向 Fluentd 服务器写入日志和（可选）指标数据。

下表描述了 `Fluentd` 节支持的配置设置。

| 名称 | 数据类型 | Description |
|------|-----------|-------------|
| `Host` | String | Fluentd 服务器的 IP 地址或 DNS 主机名。 |
| `Port` | Integer | Fluentd 服务器的端口。<br/> 默认值为 24224。 |
| `HeartbeatMs` | Integer | 检测信号间隔，以毫秒为单位。 如果在此间隔过期之前未发送事件流量，则将检测信号发送到 Fluentd 服务器。 默认值为 60000 毫秒（1 分钟）。 |
| `SendBufferSize` | Integer | 为发送操作分配的网络缓冲空间，以字节为单位。 默认值为 32768 字节（32 KB）。 |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | 与 Fluentd 服务器建立 SSL/TLS 连接的超时值，以毫秒为单位。 默认值为 10000 毫秒（10 秒）。<br/> 如果 `UseTLS` 设置为 false，则会忽略此值。 |
| `UseTLS` | Boolean | 指示容器是否应使用 SSL/TLS 来与 Fluentd 服务器通信。 默认值为 false。 |

## <a name="logging-settings"></a>日志记录设置

`Logging` 设置管理容器的 ASP.NET Core 日志记录支持。 可对容器使用用于 ASP.NET Core 应用程序的相同配置设置和值。 

LUIS 容器支持以下日志记录提供程序：

|提供程序|目的|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` 日志记录提供程序。 支持此日志记录提供程序的所有 ASP.NET Core 配置设置和默认值。|
|[调试](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` 日志记录提供程序。 支持此日志记录提供程序的所有 ASP.NET Core 配置设置和默认值。|
|[磁盘](#disk-logging)|JSON 日志记录提供程序。 此日志记录提供程序将日志数据写入输出装入点。|

### <a name="disk-logging"></a>Disk 日志记录
  
`Disk` 日志记录提供程序支持以下配置设置：  

| 名称 | 数据类型 | Description |
|------|-----------|-------------|
| `Format` | String | 日志文件的输出格式。<br/> **注意：** 此值必须设置为 `json` 才能启用日志记录提供程序。 如果指定了此值，但未同时在实例化容器时指定输出装入点，则会发生错误。 |
| `MaxFileSize` | Integer | 日志文件的最大大小，以 MB 为单位。 如果当前日志文件的大小达到或超过此值，则日志记录提供程序会启动新的日志文件。 如果指定 -1，则日志文件的大小仅受输出装入点的最大文件大小（如果有）的限制。 默认值为 1。 |

有关配置 ASP.NET Core 日志记录支持的详细信息，请参阅[设置文件配置](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration)。

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

LUIS 容器的设置是分层的，[主计算机](luis-container-howto.md#the-host-computer)上的所有容器都使用共享层次结构。

可以使用以下方法之一指定设置：

* [环境变量](#environment-variable-settings)
* [命令行参数](#command-line-argument-settings)

环境变量值替代命令行参数值，而这些参数值又替代容器映像的默认值。 如果在环境变量和命令行参数中为同一配置设置指定不同的值，则实例化的容器将使用环境变量中的值。

|优先级|正在设置位置|
|--|--|
|1|环境变量| 
|2|命令行|
|3|容器映像默认值|

### <a name="environment-variable-settings"></a>环境变量设置

使用环境变量的优点是：

* 可以配置多个设置。
* 多个容器可以使用相同的设置。

### <a name="command-line-argument-settings"></a>命令行参数设置

使用命令行参数的好处是每个容器都可以使用不同的设置。

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